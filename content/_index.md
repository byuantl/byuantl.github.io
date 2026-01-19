---
title: ""
weight: 1
# bookFlatSection: false
# bookToc: false
# bookHidden: true
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
# bookHref: ''
# bookIcon: ''
---
{{< katex />}}

{{% columns ratio="1:1.618"%}}
-  
  {{< image 
    src="headshot.jpg" 
    alt="Headshot" 
    loading="lazy" 
  >}}

- # **Hi, I'm Bowen!**
  I am an Engineering Physics student designing analog and mixed-signal systems at the intersection of physics and real-world hardware.
{{% /columns %}}

## **Featured Projects**

{{% columns %}}
- {{< card 
    href="/projects/op-amp" 
    image="/projects/op-amp/dc.png" 
  >}}
  **Two Stage Op Amp**

  Two stage CMOS op amp (gpdk045) achieving 46dB gain and 600MHz bandwidth designed and simulated in Cadence Virtuoso and ADE Assembler
  {{< /card >}}

- {{< card 
    href="/projects/self-driving" 
    image="/projects/self-driving/pedestrian_detection.png" 
  >}}
  **Simulated Self-driving Agent**

  Self-driving agent navigating simulated ROS Gazebo environment built with computer vision stack including custom CNN for OCR and sign recognition.
  {{< /card >}}
{{% /columns %}}

{{< button href="/projects" >}}See More...{{< /button >}}