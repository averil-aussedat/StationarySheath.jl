# StationarySheath

[![Build Status](https://github.com/averil-prost/StationarySheath.jl/actions/workflows/CI.yml/badge.svg?branch=main)](https://github.com/averil-prost/StationarySheath.jl/actions/workflows/CI.yml?query=branch%3Amain)

Fixed-point algorithm for stationary solutions of the Vlasov-Poisson system. 

Resolution in ``(x,v) \in [0,1] \times \mathbb{R}`` with 
  * homogeneous Dirichlet and Neumann boundary conditions at ``x=0`` for the electrostatic potential ``\phi``, inspired from the research of symmetric solutions on ``x\in[-1,1]``,
  * non-emitting boundary conditions at ``x=1`` for the kinetic densities ``f_i`` and ``f_e``,
  * ionization term.

Discretization of ``\phi`` in a finite-dimensional space. The fixed-point iterates
  * computation of ``n_e = \int_{v\in\mathbb{R}} f_e(x,v) dv`` with a quadrature formula.
  * computation of ``n_i = \int_{v\in\mathbb{R}} f_i(x,v) dv`` with Duhamel's principle.
  * resolution of the Poisson problem ``- \lambda^2 \phi'' = n_i - ne``.

The code may be run with 
```bash
git clone https://github.com/averil-prost/StationarySheath.jl
cd StationarySheath.jl
julia --project
julia> using Pkg
julia> Pkg.instantiate()
julia> include("example/main.jl")
```

If you want to develop the package, use Revise.jl
```
cd DynamicElectricSheath.jl
julia --project
julia> using Revise
julia> using DynamicElectricSheath
```

The module is rebuilt after every change in src directory.