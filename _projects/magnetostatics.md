---
layout: page
title: Magnetostatics
description: The magnetic field of current-carrying coils by geometric multigrid and GPU Biot–Savart, plus a relativistic Boris pusher for charged-particle motion — cyclotron orbits, drifts, and a magnetic-mirror trap.
img: assets/img/projects/magnetostatics/magnetic_bottle_field.png
importance: 1
category: Electrodynamics
related_publications: false
---

The first of three electrodynamics solvers on the C++/OpenGL simulation
framework, ported from a Python prototype and validated against analytic
results: the static magnetic field of a coil configuration, computed two
independent ways, feeding a relativistic test-particle pusher.

## Coil fields: multigrid and Biot–Savart

The magnetic field of a set of current-carrying coils, two ways. A
**geometric multigrid** solver for the vector-potential Poisson equation
$\nabla^2 \mathbf{A} = -\mu_0 \mathbf{J}$ on a grid, and a direct
**Biot–Savart** sum over the coil filaments evaluated on the GPU for a
slice plane. The multigrid W-cycle converges in a grid-independent
number of cycles (~8, flat from $128^2$ to $1024^2$) once the inter-grid
transfers are transpose-compatible and the reflective-ghost boundary is
applied on every level.

{% include figure.liquid path="assets/img/projects/magnetostatics/multigrid_scaling.png" title="Multigrid scaling" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The multigrid V-cycle count stays flat with grid size (128² → 1024²) while optimally-tuned SOR degrades — the payoff of the grid-independent W-cycle, and what makes a fine field grid affordable.
</div>

## A relativistic particle in a magnetic bottle

Feeding the Biot–Savart field into a **relativistic Boris pusher** (state
carried as proper velocity $\mathbf{u} = \gamma \mathbf{v}$) turns it
into a test-particle sandbox: cyclotron motion, $\mathbf{E} \times
\mathbf{B}$ drift, and the magnetic bottle — two coils carrying current
in the same sense, a charge with a large pitch angle bouncing between
the high-field throats while the adiabatic invariant $\mu = v_\perp^2 /
B$ is conserved bounce to bounce.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_bottle_field.png" title="Magnetic bottle field" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/bottle_orbit.png" title="Bottle orbit conservation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: the mirror field of the two coils (Biot–Savart, xz slice) with a trapped particle's trajectory threaded through it. Right: the particle's relativistic kinetic energy is exactly conserved (static B does no work) and the adiabatic invariant μ holds to 3.5% bounce-to-bounce.
</div>

{% include video.liquid path="assets/video/projects/magnetostatics/magnetic_bottle.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The same run animated: released near the mid-plane with v_perp > v_par, the particle spirals toward one throat, reflects where the field is strong enough, and bounces back — the confinement mechanism of the Van Allen belts and mirror-machine fusion devices." %}

## Code

- OpenGL Physics (`09_magnetostatics`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
