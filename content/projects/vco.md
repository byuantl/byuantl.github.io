---
title: "45nm Voltage Controlled Oscillator"
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
# **45nm Voltage Controlled Oscillator**
{{< image 
  src="/projects/vco/vco.png" 
  alt="VCO Screenshot" 
  loading="lazy" 
>}}
# **Overview**
Fully-integrated CMOS Voltage Controlled Oscillator (VCO) operating at 5.25 GHz using 45 nm PDK models. The design achieves a tuning range >10%, VCO gain >900 MHz/V, and low phase noise (< -114.6 dBc/Hz @ 600 kHz, < -119.3 dBc/Hz @ 1 MHz), while maintaining low power consumption (15.3 mW) from a 1.2 V supply.
# **Highlights**
- RF Analog Fundamentals, Oscillator Theory (Phase Noise, Leeson’s Model, Negative Resistance, LC Tank Design)
- Advanced RF Simulation (PSS, Pnoise, Transient, Parametric Sweeps, Noise Contribution Analysis) 
- 45 nm GPDK Analog CMOS IC Design, EDA Tools (Cadence Virtuoso/Spectre/ADE Assembler, LTspice) 
- Power Optimization Techniques (gm/Id design, bias scaling, startup margin tuning)
- **Cross-coupled LC VCO topology** with MOS varactor-based tuning network enabling wideband frequency control from 4.65 GHz to 5.79 GHz (>10% tuning range)
- **High VCO gain (K_VCO ≈ 948 MHz/V)** with localized linear region exceeding 1.24 GHz/V through optimized varactor sizing and capacitance allocation
- **Low phase noise performance** achieving -114.6 dBc/Hz @ 600 kHz and -119.3 dBc/Hz @ 1 MHz via high-Q LC tank design and noise-aware biasing
- **Fast startup time (< 2 ns)** validated via transient simulation with stable differential output swing
- **Power-efficient design (15.3 mW)** achieved through iterative transistor sizing, tail current optimization, and minimizing unnecessary negative resistance margin
---
# **Details**
{{< pdf src="/projects/vco/Bowen_Yuan_VCO_Report.pdf#view=Fit&page=1" height="85vh" >}}