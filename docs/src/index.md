
# Stationary Sheath

Implementation of a fixed-point algorithm for 
the stationary Vlasov-Poisson system.

```math
    \begin{cases}
        v \partial_x f_e(x,v) + \frac{1}{\mu} \phi''(x) \partial_v f_e(x,v) = 0 \\\
        v \partial_x f_i(x,v) - \phi'(x) \partial_v f_i(x,v) = \nu f_e(x,v) \\\
        - \lambda^2 \phi''(x) = n_i(x) - n_e(x)
    \end{cases}
```
with the densities ``n_s``, ``s\in\{i,e\}`` defined as
```math
    n_s(x) = \int_{v\in\mathbb{R}} f_s(x,v) dv.
```

Code initiated during CEMRACS 2022 by [Michel Mehrenberger](http://www.i2m.univ-amu.fr/perso/mehrenberg.m/), [Anaïs Crestetto](https://www.math.sciences.univ-nantes.fr/~crestetto/), [Mehdi Badsi](https://www.math.sciences.univ-nantes.fr/~badsi/) and [Averil Prost](https://github.com/averil-prost).
Julia support by [Pierre Navaro](https://github.com/pnavaro).

## Contents

```@contents
Modules = [StationarySheath]
Pages = ["Types.md", "Eval.md", "Algo.md", "Diags.md"]
```

## Index

```@index
Modules = [StationarySheath]
```