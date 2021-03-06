# pyModelChecking
pyModelChecking is a simple Python model checking package. Currently, it is able to represent
[Kripke structures][Kripke], [CTL][CTL], [LTL][LTL], and [CTL*][CTLS] formulas and
it provides [model checking][modelchecking] methods for LTL, CTL, and CTL*.
In future, it will hopefully support symbolic model checking.

[Kripke]: https://en.wikipedia.org/wiki/Kripke_structure_%28model_checking%29
[CTL]: https://en.wikipedia.org/wiki/Computation_tree_logic
[modelchecking]: https://en.wikipedia.org/wiki/Model_checking
[LTL]: https://en.wikipedia.org/wiki/Linear_temporal_logic
[CTLS]: https://en.wikipedia.org/wiki/CTL*

### A simple example
First of all, we built a Kripke structure.

```python
>>> from pyModelChecking import *

>>> K=Kripke(R=[(0,0),(0,1),(1,2),(2,2),(3,3)],
...          L={0: set(['p']), 1:set(['p','q']),3:set(['p'])})
```

Then, we imported the CTL temporal language and we tried to build
the two CTL formulas

```python
>>> from pyModelChecking.CTL import *

>>> phi=AU(True,Or('q',Not(EX('p'))))

>>> print(phi)

A(True U (q or not (EX p)))

>>> psi=A(F(G('p')))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pyModelChecking/CTL/language.py", line 41, in __init__
    self.wrap_subformulas(phi,StateFormula)
  File "pyModelChecking/CTLS/language.py", line 49, in wrap_subformulas
    raise TypeError('%s must be a %s' % (phi,FormulaClass.__name__))
TypeError: G p must be a StateFormula
```

Apparently, $AFG p$ is not a CTL formula (F() takes only state formulas as
parameters).

We found the states of $K$ that model the formula $\phi$.

```python
>>>  modelcheck(K,phi)

set([1, 2])
```

Since $AFG p$ is a LTL formula, we imported the LTL temporal language and
we used LTL model checking to establish which states of $K$ satisfy it.

```python
>>> from pyModelChecking.LTL import *

>>> psi=A(F(G('p')))

>>> print(phi)

A(G(F(p))

>>> modelcheck(K,phi)

set([3])
```

We built two CTL* formulas.

```python
>>> from pyModelChecking.CTLS import *

>>> eta=A(F(E(U(True,Imply('p',Not('q'))))

>>> print(eta,eta.__class__)

(A(F(E((True U (p --> not q))))), <class 'pyModelChecking.CTLS.language.A'>)

>>> rho=A(G(And(X('p'),'p')))

>>> print(rho,rho.__class__)

(A(G((X(p) and p))), <class 'pyModelChecking.CTLS.language.A'>)
```

Finally, we tried to model checking them.

```python
>>> import pyModelChecking.CTL as CTL

>>> CTL.modelcheck(K,eta)

set([0, 1, 2, 3])

>>> CTL.modelcheck(K,rho)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pyModelChecking/CTL/model_checking.py", line 183, in modelcheck
    raise RuntimeError('%s is not a CTL formula' % (formula))
RuntimeError: A(G((X(p) and p))) is not a CTL formula

>>> import pyModelChecking.LTL as LTL

>>> LTL.modelcheck(K,rho)

set([3])
```

### Copyright and license

pyModelChecking
Copyright (C) 2015  Alberto Casagrande <acasagrande@units.it>

pyModelChecking is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA.
