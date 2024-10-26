---
layout: default
---

# 3D Printed Milling Machine Mounting Bracket

### Overview

This was a quarter long project during which I and another teammate co-engineered a milling machine bit mounting bracket. The idea was to create a bracket that could be 3D printed for rapid production and replacement, while still being capable of holding a milling machine bit undergoing standard machining operations.

### Design

Our final product had to meet the following design requirements:

1. With a milling machine bit mounted in the bracket and 100N of force applied at the end in either the x, y, or z directions, the end of the bit should deflect less than 2mm from its starting position in any direction.
2. 3D printable, made out of Nylon 12.
3. Less than 
4. Maintains a rectangular opening at the center of the bracket to house sensing equipment, such as an accelerometer for vibration testing.

[first iteration image]

Calculations...

Our bracket was designed across multiple iterations -- with each, we ran a weight calculation based on the volume of our model and the density of Nylon 12, as well as ran finite element analyses using Siemen's NX. For our FEAs we used a simple beam model in place of a milling machine bit and measured deflection based on forces applied in all directions at the end of the beam. After each analysis, we cut weight where possible and added strength where needed.

[FEA image] 

We also incorporated general modeling guidelines, such as fillets to avoid sharp corners and concentration stresses, as well as 

In order to make our part 3D printable, 

### Results



[back](./)
