---
layout: default
---

# Physics Simulations

### Overview

This is a series of 2D mechanical dynamics simulations, coded and animated from scratch in Python. Most are pendulum-based, but the final is a "jack-in-box" simulation in which an unconstrained, unforced jack is trapped within a shaking box. Click the link below to watch the demo video, and scroll down to view a high-level overview of the code for the final simulation -- which is essentially a more complex version of earlier entries.

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

Then you calculate the lagrangian with L = KE - V:
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

We then setup and solve the EL equations, although this time the EL equations corresponding to ybox and thbox are set to F1 and F2 respectively as opposed to 0, to add in the external force:
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

For the constraints, you essentially need to come up with a ‘phi’ for each frame interacting with another frame – so for example, “phi_aa” is the constraint for wall A interacting with / being impacted by corner A on the jack. You end up having 16 'phi’s total, where each one is the inverse of the respective box wall transformation multiplied by the respective jack corner transformation, then taking either “3” or “7” of the resulting matrix depending on if we are looking at an “x impact” or “y impact”:
```python
#first 'x' impacts
phi_aa = (se3_inverse(g_wba) * g_wja)[3]
phi_ab = (se3_inverse(g_wba) * g_wjb)[3]
phi_ac = (se3_inverse(g_wba) * g_wjc)[3]
phi_ad = (se3_inverse(g_wba) * g_wjd)[3]

phi_ca = (se3_inverse(g_wbc) * g_wja)[3]
phi_cb = (se3_inverse(g_wbc) * g_wjb)[3]
phi_cc = (se3_inverse(g_wbc) * g_wjc)[3]
phi_cd = (se3_inverse(g_wbc) * g_wjd)[3]

#now 'y' impacts
phi_ba = (se3_inverse(g_wbb) * g_wja)[7]
phi_bb = (se3_inverse(g_wbb) * g_wjb)[7]
phi_bc = (se3_inverse(g_wbb) * g_wjc)[7]
phi_bd = (se3_inverse(g_wbb) * g_wjd)[7]

phi_da = (se3_inverse(g_wbd) * g_wja)[7]
phi_db = (se3_inverse(g_wbd) * g_wjb)[7]
phi_dc = (se3_inverse(g_wbd) * g_wjc)[7]
phi_dd = (se3_inverse(g_wbd) * g_wjd)[7]
```
All of these 'phi’s get compiled into a matrix.

You can then set up the impact expressions, defining dummy variables to substitute in:
```python
#impact expressions
ham = (dLdqdot.T*qdot)[0] - L[0]
ham_d = ham.subs(dummies)

dLdqdot_d = dLdqdot.subs(dummies)

Phi = sym.Matrix([phi_aa, phi_ab, phi_ac, phi_ad, phi_ca, phi_cb, phi_cc, phi_cd, phi_ba, phi_bb, phi_bc, phi_bd, phi_da, phi_db, phi_dc, phi_dd])
dPhidq_d = Phi.jacobian([xbox_d, ybox_d, thbox_d, xjack_d, yjack_d, thjack_d])
```

You can then do the same thing, but for the impact expressions at tau+:
```python
#impact expressions plus
ham_plus = ham_d.subs(dummies_plus)
dLdqdot_plus = dLdqdot_d.subs(dummies_plus)
dPhidq_plus = dPhidq_d.subs(dummies_plus)
```

Finally, we setup our equations, where dLdqdotplus - dLdqdot = lambda * dPhidq, and ham_plus - ham = 0. You need an iterative setup for this that goes through 16 times, as there are 16 impact constraints to cycle through:
```python
impacts = []
for i in range(16):
  eqn = sym.Eq(sym.Matrix([lamda*dPhidq_d[i, 0], lamda*dPhidq_d[i, 1], lamda*dPhidq_d[i, 2], lamda*dPhidq_d[i, 3], lamda*dPhidq_d[i, 4], lamda*dPhidq_d[i, 5], 0]),
              sym.Matrix([dLdqdot_plus[0] - dLdqdot_d[0], dLdqdot_plus[1] - dLdqdot_d[1], dLdqdot_plus[2] - dLdqdot_d[2], dLdqdot_plus[3] - dLdqdot_d[3], dLdqdot_plus[4] - dLdqdot_d[4], dLdqdot_plus[5] - dLdqdot_d[5], ham_plus - ham_d]))
  impacts.append(eqn)
```

For the impact update laws, you need an impact condition and an impact update function.

The impact condition is fairly basic: it cycles through every phi function, and if a 0 is returned (well, actually if a number between .1 and -.1 is returned, having phi be exactly 0 is too sensitive and would not be detected) then we know an impact has occurred. When an impact has occurred, the impact condition function returns a 1, signaling an impact, and the value ‘i’ so that we know under which phi function / constraint the impact happened at:
```python
funcphi = sym.lambdify([xbox_d, ybox_d, thbox_d, xjack_d, yjack_d, thjack_d,
                        xboxdot_d, yboxdot_d, thboxdot_d, xjackdot_d, yjackdot_d, thjackdot_d], Phi)

def impact_condition(s, funcphi):
  for i in range(16):
    if (funcphi(*s)[i] < .1): #change .1 to change 'tolerance' or sensitivity to impact
      if (funcphi(*s)[i] > -.1):
        return np.array([1, i]) #return i so we know where impact occurred
  return np.array([0, 0])
```

In the impact update function, we substitute in the current state of the system (‘s’) into a particular impact function (chosen by ‘i’ in the impact condition function). The impact function is then solved to get a series of new states of the system. We then cycle through each state, and we know we have the correct one when you find one with an absolute lambda value greater than .1 (I use .1 as a ‘tolerance’ value for the impacts). The entire state of the system is then returned, being q (which doesn’t change) and the new qdots:
```python
def impact_update(s, impacts):
  #replace dummies with values
  impacts_new = impacts.subs([(xbox_d, s[0]), (ybox_d, s[1]), (thbox_d, s[2]), (xjack_d, s[3]), (yjack_d, s[4]), (thjack_d, s[5]),
                              (xboxdot_d, s[6]), (yboxdot_d, s[7]), (thboxdot_d, s[8]), (xjackdot_d, s[9]), (yjackdot_d, s[10]), (thjackdot_d, s[11])])
  #solve
  solution_new = sym.solve(impacts_new, [xboxdot_plus, yboxdot_plus, thboxdot_plus, xjackdot_plus, yjackdot_plus, thjackdot_plus, lamda])
  for i in range(len(solution_new)):
    if abs(solution_new[i][6]) > .1:
      return np.array([s[0], s[1], s[2], s[3], s[4], s[5],
                     solution_new[i][0],
                     solution_new[i][1],
                     solution_new[i][2],
                     solution_new[i][3],
                     solution_new[i][4],
                     solution_new[i][5]])
```

[back](./)
