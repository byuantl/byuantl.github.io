---
title: "45nm Low Noise Amplifier"
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
# **45nm Low Noise Amplifier**
{{< image 
  src="/projects/lna/lna.png" 
  alt="LNA Screenshot" 
  loading="lazy" 
>}}
# **Overview**
Fully-integrated CMOS Low Noise Amplifier (LNA) for 5.25 GHz wireless applications using 45nm PDK models. The LNA meets stringent RF specifications including bandwidth of 200 MHz, gain >15 dB, noise figure <2.063 dB, input/output matching <-10 dB, and IIP3 > -7 dBm, while minimizing power consumption from a 1.0V supply.
# **Highlights**
- Analog Fundamentals, Analog Simulation (S-parameter Analysis, Noise Analysis, Linearity Analysis, AC/DC/Transient Analysis, Monte-Carlo, etc.) 
- 45 nm GPDK Analog CMOS IC Design, EDA Tools (Cadence Virtuoso/Spectre/ADE Assembler, LTspice) 
- Power Distribution, Layout and Physical Design Fundamentals 
- **Inductively-degenerated cascode topology** with optimized 125μm/120μm transistor widths achieving 34.96 mS and 36.07 mS transconductance
- **Precision output matching network** designed using tapped capacitor method with Cp = 1.19 pF and Cs = 395 fF to interface with 50 fF load capacitance
- **Simultaneous power and noise matching** achieved through careful selection of Lg = 2.85 nH and Ls = 0.34 nH, resulting in NF of 2.003 dB at 5.25 GHz
- **Superior reverse isolation** of -41.52 dB from cascode configuration, well below the -30 dB requirement
- **Optimized power consumption** of only 2.862 mW, achieved through iterative transistor sizing and biasing optimization while maintaining all specifications
---
# **Details**
{{< pdf src="/projects/lna/Bowen_Yuan_LNA_Report.pdf#view=Fit&page=1" height="85vh" >}}