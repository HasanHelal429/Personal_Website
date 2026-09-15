---
layout: page
title: Computational Electromagnetics
description: A 2D finite-difference time-domain (FDTD) solver for the full Maxwell curl equations — CPML absorbing boundaries, a plane-wave source, materials, and near-to-far-field transforms validated against Mie scattering and the Fresnel equations, then pushed into interference, diffraction, and a guided mode.
img: assets/img/projects/computational-electromagnetics/double_slit_thumb.png
importance: 2
category: Electrodynamics
related_publications: false
---

A 2D FDTD solver for the full Maxwell curl equations on a Yee grid —
validated against Mie scattering, antenna patterns, and the Fresnel
equations, then used to render interference, diffraction, and a guided
mode.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/computational-electromagnetics/fdtd_dipole.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/fdtd_greens.png" title="2D Green's function match" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A continuous-wave line source radiating into an absorbing (CPML) box, animated (left) and frozen in steady state (right) — the field matches the 2D scalar Green's function to a 0.9997 correlation.
</div>

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
    A PEC cylinder's Mie scattering pattern, a driven half-wave dipole, and Fresnel reflection/refraction at a dielectric interface — each checked against its classic analytic prediction.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/pulse_mur.png" title="Mur boundary" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/computational-electromagnetics/pulse_cpml.png" title="CPML boundary" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Same pulse, same grid, only the boundary changed. Left (Mur): a real reflection returns and builds a visible interference pattern. Right (CPML): the domain stays quiet, at -81dB absorption.
</div>

{% include figure.liquid path="assets/img/projects/computational-electromagnetics/lens_shadow.png" title="Dielectric lens and PEC shadow" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A point source's wavefronts pass through a dielectric cylinder (a crude lens) and converge just past it, then run into a PEC strip, which casts a sharp shadow and reflects a wake back toward the source.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/computational-electromagnetics/double_slit.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/computational-electromagnetics/diffraction_grating.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: a plane wave through two slits — the classic double-slit interference pattern, built from Huygens wavelets. Right: five evenly-spaced slits fan the field into distinct diffraction orders instead of one continuous fringe pattern.
</div>

{% include video.liquid path="assets/video/projects/computational-electromagnetics/waveguide.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A point source in a narrow parallel-plate channel: outside, ordinary circular wavefronts; inside, a single clean guided mode marching down the channel instead of radiating outward." %}

## Code

- OpenGL Physics (`10_fdtd`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
