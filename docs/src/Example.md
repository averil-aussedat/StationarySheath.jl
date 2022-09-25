# Example

Setting the simulation parameters. 

  * `params` contains the physical constants, the meshes parameters and the number of iterations.
  * `phi` is the electrostatic potential. Current implementation covers 
    + `grid` representation, by storing the value on a regular 1d mesh
    + `poly` representation, by decomposition in a polynomial basis.
  * `f_eb` is the boundary condition at ``(x=0,v\leqslant 0)``.

```@example test
using StationarySheath
using Plots

eps = 1e-10
params = Params(Nx=100,Nve=100,Nvi=100) # only keywords arguments for this one
# phi = Phidata_grid(params.meshx)
phi = Phidata_poly(5, "pair", params.meshx)
f_eb = Feb(phi,params)
```

Allocation of program variables. 

```@example test
# physical quantities
ni = zeros(params.meshx.NN+1)
ne = zeros(params.meshx.NN+1)
phix  = eval_phi(phi, params, params.meshx.mesh) # eval of phi on meshx
phidx = eval_phi_dx(phi, params, params.meshx.mesh) # eval of phi' on meshx

# allocations of temporary variables
vi     = zeros(params.meshx.NN+1) # temp velocities along ion char
vip    = zeros(params.meshx.NN+1) # temp velocities along ion char (previous)
chari  = zeros(params.Nvi+1) # temp velocities at x=0 for ions
chare  = zeros(params.Nve+1) # temp velocities at x=z for electrons
tointe = zeros(params.Nve+1) # electron velocities to integrate
toint  = zeros(params.meshx.NN+1) # to integrate on space
phixp  = copy(phix) # previous phix, for stopping criterion
```

Main loop. 

```@example test
goOn = true 
n = 0
while (goOn && n<params.Nit_max)

    compute_ni!(ni, chari, vi, vip, toint, params, f_eb, phix, phidx)
    compute_ne!(ne, chare, tointe, params, f_eb, phix)
    solve_Poisson!(phi, params, ni, ne)
    
    phix  .=    eval_phi(phi, params, params.meshx.mesh)
    phidx .= eval_phi_dx(phi, params, params.meshx.mesh)

    global goOn = diags_loop(params, phi, ni, ne, phix, phixp, n, eps, verbose=(mod(n,20)==0))
    phixp .= phix
    f_eb.Le_threshold = 𝓛ₑ(params, phix[end], 0.0)

    global n += 1

end # main loop
```

The evaluation of ``\phi`` is always available in `phix`, regardless of the representation choice.

```@example test
plot(params.meshx.mesh, phix, legend=false, title="Electrostatic potential", xlabel="x")
```

Spatial densities may be plotted directly.

```@example test
p = plot(layout=(1,2), size=(800,300))
plot!(p[1], params.meshx.mesh, ni, legend=false, title="Ion density")
plot!(p[2], params.meshx.mesh, ne, legend=false, title="Electron density")
```

The kinetic densities ``f_i`` and ``f_e`` may be reconstructed for plotting. 
The computation domain is restricted to ``(x,v) \in [0,1] \times \mathbb{R}_-``, since the 
values of ``f_i`` and ``f_e`` may be deduced in ``[-1,1] \times \mathbb{R}`` using 
```math
f_e(x,v) + f_e(x,-v) = 0, 
\quad \frac{d}{dt} [f_i(x(t),v(t)) + f_i(x(t),-v(t))] = 0
\quad \text{with } \frac{d}{dt}\mathcal{L}_i (x(t),v(t)) = 0, \\
f_s(x,v) + f_s(-x,-v) = 0 \quad \text{for } s \in\{i,e\}.
```

```@example test
lowerbound = get_v_char_e(params, 0.0, phix[end], 0.0)
fe = zeros(params.Nve+1, params.meshx.NN+1) # regular grid evaluation
fi = zeros(params.Nvi+1, params.meshx.NN+1) # regular grid approximation

build_fe!(fe, params, f_eb, phix, lowerbound)
build_fi!(fi, params, f_eb, phix, phidx, lowerbound)

p = plot(layout=(1,2), size=(800,300))
heatmap!(p[1], params.meshx.mesh, LinRange(lowerbound,0.0,params.Nve+1), fi, title="f_i")
heatmap!(p[2], params.meshx.mesh, LinRange(lowerbound,0.0,params.Nvi+1), fe, title="f_e")
```