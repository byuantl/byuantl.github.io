---
title: "PCB Etching at Home"
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
# **PCB Etching at Home**
{{< image 
  src="/projects/pcb-etching/finalpcb.png" 
  alt="Final Etched PCB" 
  loading="lazy" 
>}}
# **Overview**
In-house PCB etching enabling rapid prototyping for a CanSat project, cutting traditional manufacturer lead times and costs to accelerate design iterations.
# **Highlights**
- Rapid Prototyping, Manufacturing
- EDA Tools (Altium/KiCad/EAGLE)
- Hazardous Chemical Handling
---
# **Details**
Check out the CanSat project that this custom PCB was made for on [Github](https://github.com/ryanziyue/enph253)!
{{% columns ratio="1.618:1"%}}
- ## **1. Design the PCB**
  Create the schematic and board layout using EDA software like Altium, KiCad, or EAGLE, ensuring all traces are correctly routed for your circuit.

-  
  {{< image 
    src="/projects/pcb-etching/eda.png" 
    alt="EDA screenshot" 
    loading="lazy" 
  >}}
{{% /columns %}}
{{% columns ratio="1:1.618"%}}
-  
  {{< image 
    src="/projects/pcb-etching/layout.jpg" 
    alt="Printed layout" 
    loading="lazy" 
  >}}

- ## **2. Print Trace Layout**
  Print the board layout design onto glossy paper using a laser printer set to its highest quality and maximum toner density for a solid, dark image.
{{% /columns %}}
{{% columns ratio="1.618:1"%}}
- ## **3. Transfer Layout to Copper**
  Use a clothing iron to transfer the printed design onto a pre-cleaned copper board, melting the toner onto the copper surface which acts like a negative photoresist.

-  
  {{< image 
    src="/projects/pcb-etching/transfer.png" 
    alt="Layout transfer" 
    loading="lazy" 
  >}}
{{% /columns %}}
{{% columns ratio="1:1.618"%}}
-  
  {{< image 
    src="/projects/pcb-etching/etchant.png" 
    alt="Etchant solution" 
    loading="lazy" 
  >}}
  
- ## **4. Dissolve Unwanted Copper with Etchant**
  Submerge the board in an etchant solution (8% $HCl$ and 12% $H_2 O_2$ in a 9:1 ratio) to dissolve all copper not protected by the negative photoresist.
{{% /columns %}}
{{% columns ratio="1.618:1"%}}
- ## **5. Clean Toner with Acetone**
  Thoroughly wash the board with water and use acetone to completely scrub away the toner with a cloth, revealing the clean copper traces beneath.

-  
  {{< image 
    src="/projects/pcb-etching/cleaned.jpg" 
    alt="Cleaning board with acetone" 
    loading="lazy" 
  >}}
{{% /columns %}}
{{% columns ratio="1:1.618"%}}
-  
  {{< image 
    src="/projects/pcb-etching/holes.png" 
    alt="Drill holes into PCB" 
    loading="lazy" 
  >}}
  
- ## **6. Drill and Sand Holes for Components**
  Carefully drill holes for component leads using a small drill or hammer and nail, then lightly sand the holes to remove any burrs for easy soldering.
{{% /columns %}}






