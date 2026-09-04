---
layout: page
title: Electrodynamics
description: Magnetostatics, full-wave Maxwell, and the fields of accelerating charges — coil fields and particle traps, radiation and scattering, and self-consistent radiative dynamics.
img: assets/img/projects/electrodynamics/synchrotron.png
importance: 2
category: Electrodynamics
related_publications: false
chart:
  plotly: true
---

Three electrodynamics solvers, all built on the same C++/OpenGL
simulation framework used for the [relativistic hydrodynamics
work]({{ '/projects/general-relativity/' | relative_url }}): a magnetostatics solver
(vector-potential Poisson + Biot–Savart), a full-wave 2D Maxwell FDTD
solver, and a Liénard–Wiechert code for the fields of moving point
charges. Each was ported from a Python prototype to run interactively
on the GPU, and each ships with a validation gate against an analytic
result.

Everything here is *vector* electrodynamics — fields, forces,
radiation. The scalar wave-optics methods (Helmholtz, beam
propagation, Fresnel–Kirchhoff diffraction) belong to a separate Optics
page.

## Magnetostatics: coil fields and particle traps

The magnetic field of a set of current-carrying coils, two ways. A
**geometric multigrid** solver for the vector-potential Poisson
equation $\nabla^2 \mathbf{A} = -\mu_0 \mathbf{J}$ on a grid, and a
direct **Biot–Savart** sum over the coil filaments evaluated on the GPU
for a slice plane. The multigrid W-cycle converges in a
grid-independent number of cycles (~8, flat from $128^2$ to $1024^2$)
once the inter-grid transfers are transpose-compatible and the
reflective-ghost boundary is applied on every level.

{% include figure.liquid path="assets/img/projects/electrodynamics/multigrid_scaling.png" title="Multigrid scaling" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The multigrid V-cycle count stays flat with grid size (128² → 1024²) while optimally-tuned SOR degrades — the payoff of the grid-independent W-cycle, and what makes a fine field grid affordable.
</div>

Feeding the Biot–Savart field into a **relativistic Boris pusher**
(state carried as proper velocity $\mathbf{u} = \gamma \mathbf{v}$)
turns it into a test-particle sandbox: cyclotron motion, $\mathbf{E}
\times \mathbf{B}$ drift, and the magnetic bottle — two coils carrying
current in the same sense, a charge with a large pitch angle bouncing
between the high-field throats while the adiabatic invariant $\mu =
v_\perp^2 / B$ is conserved bounce to bounce.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/magnetic_bottle_field.png" title="Magnetic bottle field" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/bottle_orbit.png" title="Bottle orbit conservation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: the mirror field of the two coils (Biot–Savart, xz slice) with a trapped particle's trajectory threaded through it. Right: the particle's relativistic kinetic energy is exactly conserved (static B does no work) and the adiabatic invariant μ holds to 3.5% bounce-to-bounce.
</div>

{% include video.liquid path="assets/video/projects/electrodynamics/magnetic_bottle.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The same run animated: released near the mid-plane with v_perp > v_par, the particle spirals toward one throat, reflects where the field is strong enough, and bounces back — the confinement mechanism of the Van Allen belts and mirror-machine fusion devices." %}

## Full-wave Maxwell: radiation and scattering (FDTD)

A 2D finite-difference time-domain solver for the full Maxwell curl
equations on a Yee grid (TM$^z$: $E_z$, $H_x$, $H_y$, leapfrog in
time), with a convolutional perfectly-matched layer (CPML) that
absorbs outgoing waves at **−81 dB**, a total-field/scattered-field
plane-wave source, and per-cell materials. The `E`-update is kept in
the standard lossy-dielectric coefficient form from the start, so
dielectrics, conductors and PEC all drop in without touching the core.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/electrodynamics/fdtd_dipole.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/fdtd_greens.png" title="2D Green's function match" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A continuous-wave line source radiating into a CPML box. In steady state the extracted phasor field matches the 2D scalar Green's function (i/4) H₀⁽¹⁾(kr) to a 0.9997 complex correlation — the 1/√r amplitude falloff and the radial phase both reproduced (convention note: the sin(ωt) source in the e^−iωt convention makes the outgoing wave H₀⁽¹⁾, not H₀⁽²⁾).
</div>

Putting a scatterer in the beam and doing a near-to-far-field transform
recovers the classic results. A perfectly-conducting cylinder in a
plane wave produces a bistatic scattering pattern that matches the 2D
Mie (cylindrical-harmonic) series; a centre-fed half-wave PEC dipole
radiates the correct broadside figure-eight; and a plane wave onto a
dielectric interface obeys the Fresnel reflectance $R(\theta)$ to
within 2% from normal incidence out to 70°.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/mie_pattern.png" title="Mie scattering" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/fresnel.png" title="Fresnel reflectance" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/antenna.png" title="Half-wave dipole" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: PEC-cylinder bistatic scattering width vs the 2D Mie series (0.995 pattern correlation at ka ≈ 3.1). Middle: s-polarisation Fresnel reflectance vs incidence angle, FDTD points on the analytic curve. Right: the half-wave PEC dipole's broadside radiation pattern with deep axis nulls.
</div>

## Fields of accelerating charges (Liénard–Wiechert)

The exact field of a point charge on an arbitrary worldline, evaluated
by solving the retarded-time equation $c(t - t_{\text{ret}}) =
|\mathbf{x} - \mathbf{r}(t_{\text{ret}})|$ per grid cell on the GPU and
applying the velocity (near) + acceleration (radiation) split:

$$
\mathbf{E} = \frac{q}{4\pi\varepsilon_0}\left[
\frac{(\mathbf{n}-\boldsymbol\beta)(1-\beta^2)}{\kappa^3 R^2}
+ \frac{\mathbf{n}\times\big((\mathbf{n}-\boldsymbol\beta)\times\dot{\boldsymbol\beta}\big)}{c\,\kappa^3 R}
\right],\quad \kappa = 1 - \mathbf{n}\cdot\boldsymbol\beta.
$$

For a charge oscillating along a line this gives the dipole radiation
doughnut; two of them in an array show the interference pattern (the
element pattern times the array factor); and a charge on a circular
orbit at $\gamma = 5$ throws its radiation into a forward searchlight
that sweeps around with it — the synchrotron beam, compressed into a
half-angle $\sim 1/\gamma$.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/electrodynamics/oscillating_dipole.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/electrodynamics/synchrotron.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: an oscillating charge radiating the dipole pattern — nulls along the oscillation axis, maxima broadside, a slight forward tip from the finite velocity. Right: a charge on a circular orbit at β = 0.98 (γ = 5). The radiation collimates into a narrow forward beam that sweeps around like a lighthouse, tracing an Archimedean spiral in the field — this is how a synchrotron light source works.
</div>

Two oscillators side by side interfere, and the far-field pattern is
the single-element doughnut times the array factor — a broadside main
lobe with the side lobes set by the element spacing.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/electrodynamics/dipole_array.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/electrodynamics/dipole_array_pattern.png" title="Array factor" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: two in-phase oscillators a half-wavelength apart, their wavefronts interfering. Right: the radiation pattern measured a couple of wavelengths out (blue) against the analytic element-pattern × array-factor prediction (green).
</div>

The measured beaming half-angle scales as $1/\gamma$ and the total
radiated power as $\gamma^4$:

{% include figure.liquid path="assets/img/projects/electrodynamics/beaming.png" title="Relativistic beaming" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Forward-lobe half-angle ∝ 1/γ (fit slope −1.05) and total radiated power ∝ γ⁴ (fit slope 4.00 to four significant figures), from the exact retarded-quantity angular distribution. The 2D field grid can't resolve the sub-cell synchrotron pulse for a direct measurement — this comes from evaluating the same Liénard–Wiechert expression at infinity.
</div>

### Self-consistent radiative inspiral

Letting the charges move under each other's *retarded* fields — a
trajectory ring buffer feeds the retarded-time solve, a relativistic
pusher integrates the motion — makes radiative inspiral emerge from the
dynamics. A bound pair of opposite charges loses energy to radiation
and spirals in, exactly the way a gravitational-wave binary does.

{% include figure.liquid path="assets/img/projects/electrodynamics/two_body_inspiral.png" title="Two-body inspiral" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Left: the two charges spiralling together over ~3 orbits. Middle: the measured separation (black) tracks the analytic two-body dipole decay law (red dashed) — with the radiated power halved, because the pairwise retarded interaction without the Abraham–Lorentz self-force carries exactly half of a symmetric pair's radiation reaction. Right: the energy budget — kinetic + interaction PE + integrated radiated power — closes to under 1% of the total energy swing over the run.
</div>

A companion parameter study sweeps the initial separation and recovers
the $-\dot E \propto d^{-4}$ scaling to a fit slope of $-3.96$, with the
half-power factor holding constant across the sweep.

The same run as an interactive figure — the trajectories on the left,
the energy budget on the right, scrub or play through the inspiral:

<div id="ed-inspiral" style="width:100%;height:540px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__edInspiralLoaded) {
    window.__edInspiralLoaded = true;
    fetch("{{ '/assets/json/projects/electrodynamics/inspiral.json' | relative_url }}")
      .then(r => r.json())
      .then(fig => {
        Plotly.newPlot('ed-inspiral', fig.data, fig.layout, {responsive: true})
          .then(() => Plotly.addFrames('ed-inspiral', fig.frames));
      });
  }
});
</script>

## Code

- OpenGL Physics (C++/OpenGL simulation framework, `09_magnetostatics` /
  `10_fdtd` / `11_retarded_fields`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototypes these were ported from
