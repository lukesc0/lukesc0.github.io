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

```python
#PE
PEbox = mbox*g*(g_wb*sym.Matrix([0, 0, 0, 1]))[1] #basically mgy
PEjack = mjack*g*(g_wj*sym.Matrix([0, 0, 0, 1]))[1]
```

To calculate KE is a little more complicated, as you need to use the transformations to calculate velocity, and then set up an inertia matrix using the mass of the objects and their respective rotational inertias J:

```python
#For KE we need velocity and inertia matrix
Vbox = unhat(se3_inverse(g_wb)*g_wb.diff(t))
Vjack = unhat(se3_inverse(g_wj)*g_wj.diff(t))
#I called this 'blank' in HW7
Jbox = mbox*lbox**2
Jjack = mjack*ljack**2
Ibox = sym.Matrix([[mbox, 0, 0, 0, 0, 0], [0, mbox, 0, 0, 0 , 0], [0, 0, mbox, 0, 0, 0], [0, 0, 0, 0, 0, 0], [0, 0, 0, 0, 0, 0], [0, 0, 0, 0, 0, Jbox]])
Ijack = sym.Matrix([[mjack, 0, 0, 0, 0, 0], [0, mjack, 0, 0, 0 , 0], [0, 0, mjack, 0, 0, 0], [0, 0, 0, 0, 0, 0], [0, 0, 0, 0, 0, 0], [0, 0, 0, 0, 0, Jjack]])
```

You can then plug those values in to find KE:
```python
KEbox = .5*(Vbox.T*Ibox*Vbox)[0]
KEjack = .5*(Vjack.T*Ijack*Vjack)[0]
```

Then you calculate the lagrangian as normal, with L = KE - V:
```python
#lagrangian
L = KEbox + KEjack - PEbox - PEjack
L = sym.simplify(L)
```

The EL equations are also found relatively normally, however, in this simulation we first need to add our external forces, all of which are acting on the box:
```python
F1 = mbox*g
F2 = 500 #change values to change rotation
F_mat = sym.Matrix([0, F1, F2, 0, 0, 0])
#2nd and 3rd term because we want to affect ybox and thbox
```
F1 is equal to the force the box experiences due to gravity – this keeps it centered in our screen and ensures that it does not fall off, and allows the jack to fall onto the walls of the box.
F2 is a simple torque that causes the box to infinitely rotate, simulating shaking the box, and causing the jack to bounce.

We then setup and solve the EL equations as we always have, although this time the EL equations corresponding to ybox and thbox are set to F1 and F2 respectively as opposed to 0, to add in the external force:
```python
L = sym.Matrix([L])
dLdq = L.jacobian(q).T
dLdqdot = L.jacobian(qdot).T
dLdqdotdt = dLdqdot.diff(t)
```
```python
eqns = sym.Eq(F_mat, dLdqdotdt - dLdq)
#solve equations
solution = sym.solve(eqns, [xboxddot, yboxddot, thboxddot, xjackddot, yjackddot, thjackddot])
```



[back](./)
