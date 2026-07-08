---
title: "Fizzbot"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: true
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
# bookHref: ''
# bookIcon: ''
---
{{< katex />}}
# **Fizzbot**
{{< image 
  src="/projects/fizzbot/fizzbot.png" 
  alt="FizzGPT" 
  loading="lazy" 
>}}
## **Overview**

Fizzbot is a Discord bot trained to talk like the members of my UBC Engineering Physics cohort's
Discord server. It started as a fine-tuning exercise — take a base LLM,
retrain it on real chat logs, see if it picks up the community's voice — and
grew into a two-part applied-AI system once people started asking it real
questions.

{{< card >}}
  {{< image 
    src="/projects/fizzbot/fizzer.png" 
    alt="Fizzbot replying in the community's established voice." 
    loading="lazy" 
  >}}
  Fizzbot replying in the community's established voice.
{{< /card >}}

The persona model is a QLoRA fine-tune of Mistral-7B, trained on anonymized,
speaker-tagged Discord logs, and it's genuinely good at sounding like the
server. What it can't do — and this turned out to be the more interesting
half of the project — is answer a factual question like "when's the midterm"
without just confidently making something up. Fixing that meant building a
second system: a retrieval-augmented generation pipeline that pulls real
course data (deadlines, grading, exam info) from structured, human-editable
source files and hands it to a separate model whose job is to answer
truthfully, including saying "not scheduled yet" rather than guessing a date.

{{< card >}}
  {{< image 
    src="/projects/fizzbot/TBD.png" 
    alt="A grounded answer correctly reporting an unscheduled exam rather than guessing." 
    loading="lazy" 
  >}}
  A grounded answer correctly reporting an unscheduled exam rather than guessing.
{{< /card >}}

This document walks through both halves — the fine-tuning pipeline and the
RAG system — with a focus on the engineering decisions and failures that
shaped the final design, not just the finished architecture.

Full code, setup instructions, and file-by-file layout are in the repo's
`README.md`

**Repo:** [github.com/byuantl/fizzbot](https://github.com/byuantl/fizzbot)

---
# **Highlights**
- Fine-tuned Mistral-7B (QLoRA) on real Discord chat logs using multi-speaker
token conditioning (<S#> / <EOT>), letting one model imitate multiple
distinct people without separate per-user models.
- Built the full data pipeline from raw Discrub exports to speaker-anonymized,
context-windowed JSONL training examples, with GPU/QLoRA and CPU
smoke-test configs for fast iteration.
- Diagnosed why naively injecting retrieved facts into the fine-tuned model's
prompt doesn't work — a continuation-only model has no training signal for
"treat this text as authoritative" — and redesigned around a two-model
split instead of forcing the fix onto the wrong model.
- Built a schema-flexible RAG pipeline (PDF/DOCX → LLM extraction → vector
retrieval → grounded QA) where adding a new data field (e.g. exam location)
requires no code changes to become retrievable.
- Added a human-in-the-loop confirmation step (Discord upload → LLM extraction
→ reaction-based approval) so no AI-extracted data reaches the live system
unreviewed.
- Benchmarked local inference runtimes (transformers, llama.cpp, MLX) on
Apple Silicon, cutting response latency from multiple minutes to single-digit
seconds by switching to Metal-accelerated, quantized inference.
- Built a 5-dimension evaluation harness (extraction accuracy, retrieval
correctness, grounding/hallucination, answer quality, per-stage latency) to
catch regressions and pinpoint which layer of the system is actually failing.
- Implemented the Discord integration in Rust (serenity): mention detection,
moderator-gated file uploads, and a semaphore-queued generation pipeline
talking to the Python model services over HTTP and stdin/stdout.

---

# **Details**

## Fizzbot: A RAG-Augmented, Fine-Tuned Discord Persona Bot

**Multi-speaker LLM fine-tuning + retrieval-augmented generation, built end-to-end:
data pipeline → training → inference → RAG → a production Discord bot.**

Fizzbot started as a fine-tuned LLM that mimics how a specific Discord community
talks. It has since grown into a small applied-AI system: the persona model
handles conversational style, while a separate retrieval pipeline grounds
factual questions (assignment deadlines, grading breakdowns, exam info) in
structured, human-editable source data — without breaking character or
hallucinating a wrong date.

This document covers both halves of the project and is written to make the
engineering decisions — and the failures that shaped them — explicit, not just
the final architecture.

---

## Why this project?

Most of the interesting engineering here wasn't "fine-tune a model" — it was
everything downstream of that: building a retrieval system for a model that
turned out to be structurally incapable of using it, diagnosing why, and
redesigning around the constraint. That process — data pipeline design,
schema design for retrieval, model/runtime benchmarking, and root-causing
production failures — is the throughline of this writeup.

---

## System overview

```
┌───────────────────────┐        ┌────────────────────────────────────┐
│   Discord (Rust,      │        │   Python ML services               │
│   serenity)           │        │                                    │
│                       │        │  ┌──────────────────────────────┐  │
│  - mention detection  │───────▶│  │ Fine-tuned Mistral-7B        │  │
│  - permission checks  │        │  │ (persona / chat style)       │  │
│  - attachment intake  │        │  └──────────────────────────────┘  │
│  - reaction-based     │        │  ┌──────────────────────────────┐  │
│    confirmation UI    │◀──────▶│  │ RAG service (FastAPI)        │  │
│                       │  HTTP  │  │  - PDF/DOCX ingestion        │  │
└───────────────────────┘        │  │  - vector retrieval (Chroma) │  │
                                 │  │  - grounded QA (instruct LLM)│  │
                                 │  └──────────────────────────────┘  │
                                 └────────────────────────────────────┘
```

Two models, two very different jobs:
- A **fine-tuned, continuation-style model** (Mistral-7B via QLoRA) generates
  in-character chat — this is the original project.
- A **separate instruction-tuned model** handles retrieval-grounded factual
  answers — added because the fine-tuned model, as discussed below, is
  structurally the wrong tool for that job.

---

## Part 1 — The fine-tuning pipeline

### Data pipeline
Raw Discrub exports → normalized `{username, content, timestamp}` records →
timestamp-sorted, per-channel `(context → target)` training pairs. Usernames
are replaced with anonymized speaker tokens (`<S0>`, `<S1>`, ...) and messages
are terminated with `<EOT>`, so the model learns turn-taking and per-speaker
style without hardcoding identities into the weights. Empty, URL-only, and
mention-only messages are filtered; context never mixes across channels.

### Training
YAML-configured causal LM fine-tuning via QLoRA (4-bit, GPU) with a parallel
CPU-only config for fast smoke tests before committing to a full run.
Checkpoints are versioned under `llm/runs/<run_name>/<timestamp>/`.

### Inference
A CLI (`llm/run.py`) wraps `transformers` generation and decodes the model's
raw `<S#>` and `<EOT>` output back into readable `username: message` form. The
Discord bot spawns this as a long-running subprocess and talks to it over a
simple stdin/stdout line protocol.

### Discord bot
Written in Rust (serenity), the bot responds when mentioned, maps Discord
usernames to speaker tokens, and manages a semaphore so concurrent requests
queue rather than overload the model process.

### Examples
{{< card >}}
  {{< image 
    src="/projects/fizzbot/example-text.png" 
    alt="Example persona output" 
    loading="lazy" 
  >}}
  Example persona output
{{< /card >}}

## Part 2 — Retrieval-augmented generation

### The problem
Users wanted fizzbot to answer real questions — "when's the midterm," "how's
grading weighted" — accurately, in channels named after their courses, without
losing its personality doing it.

### The failure that shaped the design
The obvious first approach — inject retrieved facts directly into the
fine-tuned model's prompt — doesn't work, and understanding *why* was the
actual engineering problem:

The fine-tuned model's entire prompt format is `f"{speaker} {content} <EOT>"`.
It was trained purely to continue Discord-style text plausibly — it has no
training signal that ever taught it "text in this position is authoritative
context; ground your answer in it." Injecting facts into that prompt doesn't
make the model defer to them; it just adds tokens the model pattern-matches
around while still generating whatever sounds stylistically right. This holds
regardless of whether the base checkpoint was instruction-tuned to begin with,
since fine-tuning on a narrow, repetitive persona-chat distribution tends to
overwrite instruction-following behavior that isn't reinforced during that
fine-tuning (catastrophic forgetting).

**Fix:** split the responsibility. The fine-tuned model keeps doing what it's
actually good at — in-character generation. A separate, genuinely
instruction-tuned model handles retrieval-grounded answers, called only when
a message is routed to it.

### RAG pipeline
1. **Ingestion** — syllabus PDFs/DOCX are extracted to raw text, then passed
   to an instruction-tuned LLM with a strict extraction prompt: output
   structured YAML matching a course schema, mark unstated fields `TBD`, never
   guess. This can run via Discord upload directly (see below) or a CLI
   script.
2. **Human-in-the-loop confirmation** — extracted data is posted back to
   Discord for a moderator/admin to review; nothing is committed to the live
   index until a ✅ reaction confirms it. This exists specifically because LLM
   extraction is good, not perfect, and a wrong exam date is a worse failure
   than a slow review step.
3. **Schema-flexible chunking** — rather than hardcoding which YAML fields
   exist, the chunker iterates generically over whatever keys are present in
   each record and generates a natural-language sentence per fact. Adding a
   new field (e.g. exam `location`) to the source YAML makes it retrievable
   automatically, with no code change.
4. **Retrieval** — sentence-transformer embeddings + ChromaDB, filtered by
   course (auto-derived from the Discord channel name against each syllabus's
   own `course:` field — no manually maintained channel→course map to keep in
   sync).
5. **Grounded generation** — retrieved facts + a strict "answer only from
   this context, say so plainly if something is marked TBD" system prompt,
   run through a local instruct model.
6. **Discord integration** — a lightweight keyword-based intent check routes
   likely factual questions to the RAG service; everything else stays on the
   persona path. Moderator-gated file upload lets course data be added or
   updated without touching code.

### Examples
{{< card >}}
  {{< image 
    src="/projects/fizzbot/rag-example.png" 
    alt="Example RAG output" 
    loading="lazy" 
  >}}
  Example RAG output
{{< /card >}}

### Local inference and runtime benchmarking
Given hardware constraints (Apple Silicon, no CUDA), meaningful engineering
went into just getting acceptable latency:
- `transformers` on CPU was unusably slow (minutes per short answer) and, at
  one point, crashed outright with a native SIGBUS — traced to an unconditional
  4-bit `bitsandbytes` quantization config being applied regardless of device,
  a CUDA-only code path silently misbehaving on Apple hardware.
- Benchmarked `transformers` (CPU/MPS) against `llama.cpp` (Metal-accelerated,
  GGUF-quantized) and MLX (Apple's native framework) — llama.cpp with Metal
  offload and Q4 quantization gave the practically usable path for a 3–4B
  instruct model, a several-times speedup over the naive `transformers` CPU
  path.
- Sized model choice to hardware: at Q4 quantization, memory footprint scales
  roughly 0.5–0.6 GB per billion parameters, which set a practical ceiling of
  ~4B parameters on 8GB unified memory before risking swap-induced slowdowns.

---

## Evaluation

Model behavior in a system like this fails in different ways at different
stages, and "it feels wrong" isn't actionable. The eval harness checks each
stage independently:

| Dimension | What it catches | Method |
|---|---|---|
| **Extraction** | Wrong dates/fields pulled from a real syllabus | Diff against a hand-verified ground-truth YAML |
| **Retrieval** | Right facts not coming back for a question | Substring checks against expected retrieved chunks |
| **Grounding (hallucination)** | Model inventing a date/topic not in the facts | Explicit "must not contain" checks per test case, weighted as the most severe failure class |
| **Latency** | Where time is actually going (retrieval vs. generation) | Per-stage timing breakdown, not just end-to-end |

Test cases explicitly cover TBD fields (must say "not yet announced," never
invent one) and cross-channel isolation (a question in one course's channel
must not leak another course's facts) — the two failure modes most specific
to this system's design, as opposed to generic RAG QA correctness.

---

## Core lessons

1. **A model's training distribution determines what a prompt can and can't
   ask of it.** "Just add the context to the prompt" is not a universal RAG
   recipe — it depends on the target model actually having been trained to
   treat injected context as authoritative, which a raw continuation model
   never was.
2. **Retrieval schemas should describe data generically, not enumerate known
   fields.** A chunker that iterates over whatever keys exist survives schema
   evolution; one that hardcodes `due`/`topics` breaks silently the moment
   someone adds `location`.
3. **Human-in-the-loop matters more than model accuracy for irreversible
   writes.** Even a very good extraction model gets syllabus dates wrong
   sometimes; the fix isn't a better model, it's not committing unreviewed
   output to a system users trust.
4. **Hardware constraints are a design input, not an afterthought.** Runtime
   choice (llama.cpp vs. transformers vs. MLX) and quantization level were
   decided by actually profiling on the target hardware, not by defaulting to
   whatever the reference implementation used.