---
layout: page
title: Magnetostatics
description: The magnetic field of currents and coils, solved two independent ways and checked against theory, then fed into a relativistic Boris pusher — cyclotron orbits, a magnetic mirror and its loss cone, a magnetic cusp, and a toroidal (tokamak-style) coil ring.
img: assets/img/projects/magnetostatics/toroidal_ring.png
importance: 1
category: Electrodynamics
related_publications: false
---

The first of three electrodynamics solvers on the C++/OpenGL simulation
framework, ported from a Python prototype and validated against analytic
results: the static magnetic field of currents and coils, computed two
independent ways, feeding a relativistic test-particle pusher.

## Coil fields: multigrid and Biot–Savart

The magnetic field of a set of current-carrying coils, two ways. A
**geometric multigrid** solver for the vector-potential Poisson equation
$\nabla^2 \mathbf{A} = -\mu_0 \mathbf{J}$ on a grid, and a direct
**Biot–Savart** sum over the coil filaments evaluated on the GPU for a
slice plane. The multigrid W-cycle converges in a grid-independent
number of cycles — 8 at $128^2$ up to 10 at $1024^2$ — once the
inter-grid transfers are transpose-compatible and the reflective-ghost
boundary is applied on every level; a $256^2$ solve takes ~14ms and
$512^2$ ~57ms, fast enough to re-solve between frames while a wire is
being dragged. Plain SOR, by contrast, needs an iteration count that
grows with grid size — 420 at $128^2$, 1780 at $512^2$ — the whole
reason the multigrid path exists.

## Simple sources, checked against theory

Four configurations, each with a known analytic answer, exercising both
solvers:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/wire_field.png" title="Single wire field" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/solenoid_field.png" title="Solenoid field" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: a single infinite wire (2D multigrid), the classic concentric-circle field — matches B = mu0 I / 2 pi r to a median 0.3% on an interior annulus (the square domain boundary distorts the far field near the edge, expected for a finite box). Right: a solenoid cross-section, two opposite current sheets (2D, Neumann boundary) — sharply uniform between the sheets, ~0 outside; a natural (non-reflecting) far boundary gets the interior field within 1.9% of mu0 K_s, vs. 25% low with a naively closed box.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/loop_field.png" title="Current loop field" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/helmholtz_field.png" title="Helmholtz pair field" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: a single current loop (3D Biot–Savart, GPU), the classic dipole-like field-line portrait — the on-axis field matches the analytic mu0 I R^2 / [2(R^2+z^2)^(3/2)] to 3e-4. Right: a Helmholtz pair (two loops separated by their own radius) — the flat "sweet spot" between them is visibly uniform, matching the analytic centre field to 2e-5 and holding to within 6e-4 over |z| < 0.15R.
</div>

## A relativistic particle in a magnetic bottle

Feeding the Biot–Savart field into a **relativistic Boris pusher** (state
carried as proper velocity $\mathbf{u} = \gamma \mathbf{v}$) turns it
into a test-particle sandbox: cyclotron motion, $\mathbf{E} \times
\mathbf{B}$ drift, and the magnetic bottle — two coils carrying current
in the same sense, a charge with a large pitch angle bouncing between
the high-field throats while the adiabatic invariant $\mu = v_\perp^2 /
B$ is conserved bounce to bounce.

{% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_bottle_field.png" title="Magnetic bottle field" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The mirror field of two same-current coils (Biot–Savart, xz slice) with a trapped particle's trajectory threaded through it. Its relativistic kinetic energy is exactly conserved (static B does no work) and the adiabatic invariant μ holds to 3.5% bounce-to-bounce — it breathes ~10% within a single bounce near the steep-gradient turning points, the expected behaviour of an adiabatic, not exact, invariant.
</div>

{% include video.liquid path="assets/video/projects/magnetostatics/magnetic_bottle.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The same run animated: released near the mid-plane with v_perp > v_par, the particle spirals toward one throat, reflects where the field is strong enough, and bounces back — the confinement mechanism of the Van Allen belts and mirror-machine fusion devices." %}

## Cyclotron orbits and E×B drift

The two simplest pusher checks, in a uniform background field with no
coils at all. A single charge in a uniform $\mathbf{B}$ traces a circle
whose radius and period match $\gamma v_\perp / |q/m| B_0$ and $2\pi
\gamma / |q/m| B_0$ to 3e-7 and machine precision respectively, with
energy conserved to 1 part in $10^{15}$ over 20000 steps. Crossed
uniform $\mathbf{E}$ and $\mathbf{B}$ fields drift the guiding centre of
a charge — regardless of its sign — at $\mathbf{E} \times \mathbf{B} /
B^2$, matched to 1e-5.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/magnetostatics/cyclotron.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/magnetostatics/exb_drift.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: a single charge's cyclotron orbit in a uniform field. Right: two opposite charges gyrating in opposite senses in crossed E and B fields, drifting together in a straight line — the sign-independence of E x B drift, visibly.
</div>

## Confinement and its failure modes: cusp and loss cone

Two coils carrying current in the _same_ sense make a mirror; the exact
same geometry with the current in one coil reversed makes something
qualitatively different — a **cusp**. Instead of a field that's weaker
at the centre and stronger at both throats, there's a genuine magnetic
null on-axis at the midplane, with a saddle-point topology around it.
The identical trapped-particle initial condition that bounces forever in
the bottle instead finds the null and escapes radially outward through
it on the very first pass:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_bottle_field.png" title="Bottle: confines" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_cusp.png" title="Cusp: doesn't" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The same two coils, same trapped-particle initial condition, only the right-hand coil's current sign is flipped. Left (mirror): the particle bounces indefinitely. Right (cusp): the field lines meet at a saddle instead of two mirrors, and the particle escapes through the null on its first approach.
</div>

Even within the mirror, confinement isn't unconditional — it depends on
the particle's **pitch angle**, the ratio of its across-field to
along-field speed. Below a critical angle set by the mirror's
throat-to-centre field ratio, a particle's turning point would fall
_past_ the throat, so it never turns around at all: it just leaves. That
critical angle was found here by direct bisection (running the same
bottle field at successive angles and checking whether the particle's
excursion ever passes the throat) rather than assumed — trapped up to
about 34°, escaped by 34.5°, for this bottle's geometry:

{% include figure.liquid path="assets/img/projects/magnetostatics/loss_cone.png" title="Loss cone: three pitch angles" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Three charges, same speed, same bottle field, different pitch angles. Orange (45°, comfortably above the critical angle): bounces tightly, confined. Green (34°, just past the critical angle found above): still escapes, but slowly, drifting most of the way along the axis before it does. Blue (20°, well inside the loss cone): escapes promptly and directly. This is the same mechanism that empties Earth's radiation belts into the atmosphere along field lines near the poles.
</div>

## A toroidal field

Every coil configuration so far shares one axis of symmetry. A tokamak's
confining field doesn't: it's built from many coils arranged in a ring,
each one tilted so its own axis runs tangent to the ring rather than
parallel to a common one. Nothing about the Biot–Savart solver needs to
change to build this — it's the same `[[coil]]` loop primitive already
validated above, just eight of them arranged around a circle instead of
one or two on a shared axis:

{% include figure.liquid path="assets/img/projects/magnetostatics/toroidal_ring.png" title="Toroidal coil ring" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Eight loop coils on a ring, all carrying current the same sense, sliced through the plane of the ring. The field threads through the ring in a closed, roughly toroidal band — strong between the coils and inside the ring, falling off quickly outside it. There's no single symmetry axis here for an analytic on-axis check the way the single loop or Helmholtz pair have one, so this one is a qualitative demonstration of the same validated solver rather than a numerically checked result.
</div>

## Code

- OpenGL Physics (`09_magnetostatics`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
