---
title: "CSDCMS CanSat Challenge"
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
# **CSDCMS CanSat Challenge**
{{< image 
  src="/projects/cansat/cansat.jpg" 
  alt="3D Model of CanSat" 
  loading="lazy" 
>}}
# **Overview**
TurkeyBurkey CanSat is a satellite compact enough to fit inside the form factor of a soda can. Launched to an altitude of 1000 meters, collecting air temperature and pressure data during descent before transitioning to its secondary mission: measuring seismic activity upon landing. Inspired by NASA's Mars InSight mission and Vancouver's location in the seismically active Ring of Fire, we developed a system that uses an Arduino-controlled gyroscope to detect ground tremors and convert them to the Japanese Meteorological Agency's seismic intensity Shindo scale. The project encompasses mechanical design, electrical engineering, software development, and community outreach, with presentations delivered to elementary and secondary school students.
# **Highlights**
- Iterative Prototyping for Functional Integration
  - Mechanical design evolved significantly through multiple iterations
  - Initial prototypes used a standard Canduino shell focused solely on basic radio transmission testing
  - Reimagined structural design to accommodate a potentially large, space-consuming seismometer

- Innovative Structural Modification
  - Final design features a clever modification to the standard CanSat shell: metal support rods that typically remain fully encased were left exposed
  - Created flexibility, allowing opportunity to vertically position platforms using nuts, accommodating both the PCB and gyroscope at optimal heights while improving cable management throughout the compact volume

- Space-Efficient Component Layout
  - With only 115mm of height and 66mm diameter to work with, every milliliter counted in the volume budget
  - Designed a flat cutout to keep the Arduino Nano port accessible, strategically placed the barometric sensor on the bottom for air exposure while using the rest of the structure as a shield, and created a removable bottom panel for easily accesible battery replacement
---
# **Details**
{{< pdf src="/projects/cansat/BowenYuanCanSat.pdf#view=Fit&page=2" height="85vh" >}}
