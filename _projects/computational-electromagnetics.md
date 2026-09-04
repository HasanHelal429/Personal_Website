---
layout: page
title: Computational Electromagnetics
description: A 2D finite-difference time-domain (FDTD) solver for the full Maxwell curl equations — CPML absorbing boundaries, a plane-wave source, materials, and near-to-far-field transforms validated against Mie scattering and the Fresnel equations.
img: assets/img/projects/computational-electromagnetics/mie_pattern.png
importance: 2
category: Electrodynamics
related_publications: false
---

Full-wave Maxwell in the time domain: no static approximation, no
scalar reduction — the coupled electric and magnetic fields marched
forward on a staggered grid. This is the workhorse method of
computational electromagnetics, and the second of the three
electrodynamics solvers on the C++/OpenGL framework.

## The Yee scheme

A 2D finite-difference time-domain solver for the full Maxwell curl
equations on a Yee grid (TM$^z$: $E_z$, $H_x$, $H_y$, leapfrog in
time), with a convolutional perfectly-matched layer (CPML) that absorbs
outgoing waves at **−81 dB**, a total-field/scattered-field plane-wave
source, and per-cell materials. The `E`-update is kept in the standard
lossy-dielectric coefficient form from the start, so dielectrics,
conductors and PEC all drop in without touching the core.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/computational-electromagnetics/fdtd_dipole.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/fdtd_greens.png" title="2D Green's function match" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A continuous-wave line source radiating into a CPML box. In steady state the extracted phasor field matches the 2D scalar Green's function (i/4) H₀⁽¹⁾(kr) to a 0.9997 complex correlation — the 1/√r amplitude falloff and the radial phase both reproduced (convention note: the sin(ωt) source in the e^−iωt convention makes the outgoing wave H₀⁽¹⁾, not H₀⁽²⁾).
</div>

## Scattering, antennas, and the Fresnel equations

Putting a scatterer in the beam and doing a near-to-far-field transform
recovers the classic results. A perfectly-conducting cylinder in a
plane wave produces a bistatic scattering pattern that matches the 2D
Mie (cylindrical-harmonic) series; a centre-fed half-wave PEC dipole
radiates the correct broadside figure-eight; and a plane wave onto a
dielectric interface obeys the Fresnel reflectance $R(\theta)$ to within
2% from normal incidence out to 70°.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/mie_pattern.png" title="Mie scattering" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/fresnel.png" title="Fresnel reflectance" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/antenna.png" title="Half-wave dipole" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: PEC-cylinder bistatic scattering width vs the 2D Mie series (0.995 pattern correlation at ka ≈ 3.1). Middle: s-polarisation Fresnel reflectance vs incidence angle, FDTD points on the analytic curve. Right: the half-wave PEC dipole's broadside radiation pattern with deep axis nulls.
</div>

Scalar wave-optics methods (Helmholtz, beam propagation, Fresnel–Kirchhoff
diffraction) are a separate topic — that's optics, not this.

## Code

- OpenGL Physics (`10_fdtd`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
