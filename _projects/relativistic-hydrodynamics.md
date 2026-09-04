---
layout: page
title: Relativistic Hydrodynamics
description: A GPU conservative finite-volume solver for relativistic fluid on a fixed black-hole background — validated on flat spacetime, then Schwarzschild accretion, then Kerr orbits and accretion tori, with the near-horizon instability still open.
img: assets/img/projects/relativistic-hydrodynamics/fm_torus_cross_section.png
importance: 2
category: General Relativity
related_publications: false
---

Gas falling toward a black hole moves fast enough that Newtonian fluid
dynamics stops being good enough — you need the relativistic Euler
equations, on the curved metric of the hole. This is a from-scratch
solver for exactly that: fluid on a _fixed_ Schwarzschild or Kerr
background (no self-gravity, no dynamical spacetime), written as GPU
compute shaders in the same C++/OpenGL framework as the
[raytracers]({{ '/projects/black-hole-raytracing/' | relative_url }}).

## Fluid on a curved background

Real GR hydro codes are grid-based finite-volume solvers written in
_conservative_ variables — you evolve the densities of conserved
quantities (mass, momentum, energy) so that shocks come out right and
nothing is quietly created or destroyed. This one uses the standard
**Valencia formulation**: from the fluid stress-energy tensor, build

$$
D = \rho W,\qquad
S_i = \rho h W^2 v_i,\qquad
\tau = \rho h W^2 - P - D
$$

($W$ the Lorentz factor, $h$ the specific enthalpy), evolve them with a
finite-volume update, and at each step solve a small Newton iteration to
recover the primitive $(\rho, v, P)$ the fluxes need. Fluxes come from an
**HLLE** approximate Riemann solver with relativistic wave speeds. Every
piece — the conserved-variable algebra, the Riemann solver, the
curved-space source terms — was derived from first principles and
cross-checked against an independent reference before being trusted;
`docs/SIMULATION.md` in the repo is the full derivation.

## Building up: shock tubes → Bondi → Kerr

A relativistic hydro code has two parts that are easy to get subtly
wrong and impossible to debug once a black hole's metric is also in the
mix. So the solver is validated in three stages of increasing
difficulty, each against a problem with a known answer.

**Flat spacetime.** The relativistic shock tube produces the correct
wave structure — rarefaction, a genuine contact discontinuity, a shock —
with a peak velocity of 0.44 c and no oscillations. In the
low-velocity limit the same equations reduce to ordinary Newtonian
hydrodynamics: run against an exact Newtonian Riemann solver, the
profiles are indistinguishable. Mass and energy are conserved to 1e-8,
and the momentum — which should _not_ be conserved here, because the
open boundaries with unequal pressure exert a net force — matches the
predicted boundary forcing to five significant figures.

**Schwarzschild.** Spherical Bondi accretion has an exact analytic
solution. Initialise the grid to it and the solver holds it steady for
a hundred-plus free-fall times: final density within 0.6% of analytic,
the accretion rate (constant in radius for a true steady state) uniform
to 4%.

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/bondi_accretion.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Steady-state relativistic Bondi accretion onto a Schwarzschild black hole — the 1D radial density profile revolved into a plane (the flow is genuinely spherically symmetric, so this is honest, not a stylisation). Density rises toward the horizon (marked); the solver holds this analytic equilibrium indefinitely." %}

**Kerr.** Frame dragging couples radial and azimuthal motion. Restricted
to the equatorial plane — an exact invariant of the Kerr metric — the
solver tracks a ring of gas on exact circular geodesic orbits to 2.4%
over six orbital periods.

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/kerr_circular_orbit.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A ring of gas on circular geodesic orbits around a spinning (a = 0.7M) black hole, equatorial plane. Differential rotation — inner gas orbits faster — winds the ring; the spokes are a visual aid, not simulated azimuthal position." %}

## Accretion tori — and what's still open

The target is a real accretion disk. The **Fishbone–Moncrief torus** is
the standard analytic equilibrium: a donut of gas in pure circular
motion with constant specific angular momentum, its pressure gradient
exactly balancing gravity at every point. Constructing it correctly
takes the full 2D $(r,\theta)$ Kerr metric; a commonly-quoted shortcut
for it fails its own consistency check (it puts the pressure maximum in
the wrong place), so this was re-derived from the Euler equation and
validated — curl-free acceleration field, zero net force at the
pressure-maximum radius, correct crescent shape.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/relativistic-hydrodynamics/fm_torus_cross_section.png" title="Fishbone–Moncrief torus cross-section" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/accretion_torus.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: the analytic torus, a poloidal slice — the two lobes are the cross-section of a single donut-shaped ring orbiting the hole (black disk = horizon), seen edge-on. Right: the same torus handed to the dynamical solver (a = 0.9M). It holds its shape and accretes through the inner edge for several orbits.
</div>

Getting the torus to run at all took finding five separate bugs — a
wrong volume-element weighting that only diverges off the equator, a
primitive-recovery Newton solve with a float32-broken floor, conserved
states with no valid solution at all. It now completes a multi-orbit
run with no NaN, and a proper accretion-rate diagnostic confirms the
mass it loses is **real accretion through the inner boundary**, not a
numerical leak — matched to 0.05%.

What is **not** solved: the torus still eventually destabilises at the
inner radial boundary. Reformulating the whole solver in
horizon-penetrating Kerr–Schild coordinates pushed the failure back by
an order of magnitude in time but didn't remove it, and moving the grid
boundary _inside_ the horizon — now possible — makes it worse. That
points to something specific about the fluid's behaviour very close to
the horizon that isn't yet understood. It's the open problem.

## Pushing it: non-equilibrium flows

The validated solver runs plenty that isn't a tidy equilibrium. A blob
of gas dropped from rest falls in and is stretched along the way; two
narrow rings of gas on different orbits collide and shock.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/blob_infall.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/two_ring_collision.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: a gas blob released from rest at r=15M above a Kerr black hole (a=0.9M), poloidal slice — it falls in roughly radially, the arcs are its compressing leading edge. Right: two detuned rings — one under-rotating, one over-rotating — on crossing orbits, colliding into a shocked feature near periapsis.
</div>

## Code

- OpenGL Physics (`07_grhd`) — the GRHD solver and its Fishbone–Moncrief / Kerr–Schild machinery — not yet public
