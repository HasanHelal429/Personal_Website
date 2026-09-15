---
layout: page
title: Relativistic Hydrodynamics
description: A GPU conservative finite-volume solver for relativistic fluid on a fixed black-hole background — validated on flat spacetime, then Schwarzschild accretion, then Kerr orbits and accretion tori, with the near-horizon instability still open.
img: assets/img/projects/relativistic-hydrodynamics/fm_torus_cross_section.png
importance: 2
category: General Relativity
related_publications: false
---

A from-scratch GPU solver for the relativistic Euler equations on a
fixed Schwarzschild or Kerr background — the same C++/OpenGL framework
as the [raytracers]({{ '/projects/black-hole-raytracing/' | relative_url }}),
validated from flat-spacetime shock tubes up through Bondi accretion,
Kerr orbits, and full accretion tori.

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/bondi_accretion.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Steady-state relativistic Bondi accretion onto a Schwarzschild black hole — the solver holds this analytic equilibrium indefinitely." %}

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/kerr_circular_orbit.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A ring of gas on circular geodesic orbits around a spinning black hole (a=0.7M) — differential rotation winds the ring into spokes." %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/relativistic-hydrodynamics/fm_torus_cross_section.png" title="Fishbone–Moncrief torus cross-section" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/accretion_torus.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: the analytic Fishbone–Moncrief torus equilibrium, a poloidal slice. Right: the same torus handed to the dynamical solver (a=0.9M) — it holds its shape and accretes through the inner edge for several orbits before the still-open near-horizon instability eventually catches up with it.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/blob_infall.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/two_ring_collision.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: a gas blob released from rest above a Kerr black hole (a=0.9M), falling in and stretching along the way. Right: two detuned rings on crossing orbits colliding into a shocked feature near periapsis.
</div>

## Code

- OpenGL Physics (`07_grhd`) — the GRHD solver and its Fishbone–Moncrief / Kerr–Schild machinery — not yet public
