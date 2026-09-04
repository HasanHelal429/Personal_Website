---
layout: page
title: Computational Electromagnetics
description: A 2D finite-difference time-domain (FDTD) solver for the full Maxwell curl equations — CPML absorbing boundaries, a plane-wave source, materials, and near-to-far-field transforms validated against Mie scattering and the Fresnel equations, then pushed into interference, diffraction, and a guided mode.
img: assets/img/projects/computational-electromagnetics/double_slit_thumb.png
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
    A continuous-wave line source radiating into a CPML box (left, animated; right, the same field frozen in steady state). The extracted phasor field matches the 2D scalar Green's function (i/4) H₀⁽¹⁾(kr) to a 0.9997 complex correlation — the 1/√r amplitude falloff and the radial phase both reproduced (convention note: the sin(ωt) source in the e^−iωt convention makes the outgoing wave H₀⁽¹⁾, not H₀⁽²⁾).
</div>

## Scattering, antennas, and the Fresnel equations

Putting a scatterer in the beam and doing a near-to-far-field transform
recovers the classic results. A perfectly-conducting cylinder in a
plane wave produces a bistatic scattering pattern that matches the 2D
Mie (cylindrical-harmonic) series to a 0.995 pattern correlation at
$ka \approx 3.1$; a centre-fed half-wave PEC dipole radiates the
correct broadside figure-eight (0.89 correlation to the idealised
thin-wire formula, the rest being the real 2D fat-strip vs. 3D-filament
difference); and a plane wave onto a dielectric interface obeys the
Fresnel reflectance $R(\theta)$ to within 2% from normal incidence out
to 70°.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/mie_pattern.png" title="PEC cylinder scattering" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/antenna.png" title="Half-wave dipole" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/fresnel.png" title="Fresnel reflection/refraction" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: a plane wave scattering off a PEC cylinder — the forward shadow and the scattered wavefronts both visible directly in the field (|Ez| 0.15 in the shadow vs. 1.4 on the lit face). Middle: the driven half-wave dipole radiating. Right: a plane wave at oblique incidence onto a dielectric half-space (tinted region, n=2) — reflection above the interface, a shorter wavelength and a visible bend in the wavefronts (refraction) below it.
</div>

Scalar wave-optics methods (Helmholtz, beam propagation, Fresnel–Kirchhoff
diffraction) are a separate topic — that's optics, not this.

## Absorbing boundaries: Mur vs. CPML

CPML wasn't the first thing tried. A first-order Mur boundary is much
simpler, and it does let most of an outgoing pulse leave — but "most"
still means a real reflection that comes back and pollutes the
interior once a pulse has had time to cross the domain and return. The
same source, same grid, only the boundary condition changed:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/pulse_mur.png" title="Mur boundary" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/pulse_cpml.png" title="CPML boundary" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Both frames are taken after the pulse has crossed the domain and had time to return. Left (Mur): the returning reflection is large enough to build a distinct, dramatic interference pattern with the outgoing wave. Right (CPML): the domain stays quiet — the same -81dB absorption `--cpml-selftest` measures directly, made visible.
</div>

## A dielectric lens and a hard shadow

The same per-cell materials that build the Fresnel interface above
also make a crude lens: a circular region of higher permittivity bends
the wavefronts passing through it toward a focus, exactly like a glass
lens bends light. Next to it, a PEC strip does the opposite — it's a
hard reflector, casting a clean shadow.

{% include figure.liquid path="assets/img/projects/computational-electromagnetics/lens_shadow.png" title="Dielectric lens and PEC shadow" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A point source's circular wavefronts (left) pass through a dielectric cylinder (a crude lens, eps_r=2.5) and visibly converge just past it, then run into a PEC strip, which casts a sharp shadow behind it and reflects a wake back toward the source.
</div>

## Interference and diffraction

None of this needs new physics — the same validated TFSF plane-wave
source and PEC-shape machinery checked against Mie scattering above,
pointed at a different obstacle. A PEC wall with two narrow openings
turns a single plane wave into two coherent cylindrical wavelets;
downstream, they interfere. Widen the same idea to more than two
openings and the continuous fringe pattern resolves into a handful of
distinct diffraction orders instead — a grating.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/computational-electromagnetics/double_slit.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/computational-electromagnetics/diffraction_grating.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: a plane wave through two slits (three-wavelength separation) — the classic double-slit interference pattern, built from Huygens wavelets rather than assumed. Right: the same wall with five evenly-spaced slits instead of two — a diffraction grating, fanning the transmitted field out into distinct orders rather than one continuous fringe pattern.
</div>

## A guided mode: the parallel-plate waveguide

A point source dropped between two long, closely-spaced PEC plates
behaves completely differently from the same source in the open. Make
the channel narrow enough that it only supports the fundamental mode,
and the field inside stops looking like an expanding circle and starts
looking like a clean wave marching straight down the channel — guided,
not radiating.

{% include video.liquid path="assets/video/projects/computational-electromagnetics/waveguide.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A CW point source just inside a narrow (~1.2-wavelength) parallel-plate channel. Outside the plates, near the source, the field still spreads out in ordinary circular wavefronts; inside, it settles into a single clean guided mode running down the channel. A wider channel (tried first) supports many modes at once and the interior turns into a messy multi-mode interference pattern instead of this." %}

## Code

- OpenGL Physics (`10_fdtd`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
