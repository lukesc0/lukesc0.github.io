---
layout: default
---

# Physics Simulations

### Overview

This is a series of 2D mechanical dynamics simulations, coded and animated from scratch Python. Most are pendulum-based, but the final is a "jack-in-box" simulation. Click the links below to watch the demo videos, and scroll down to view a high-level overview of the code for the final simulation.

### Demo

[videos here]

### Code

To calculate the Euler-Lagrange equations, I first defined overall transformations to each frame. We then calculate PE as you would normally, as m * g * y, however this time written in terms of the transformations to the center of the objects, g_wb(for the box) and g_wj(for the jack):

'''python
#PE
PEbox = mbox*g*(g_wb*sym.Matrix([0, 0, 0, 1]))[1] #basically mgy
PEjack = mjack*g*(g_wj*sym.Matrix([0, 0, 0, 1]))[1]
'''

[back](./)
