---
layout: page
title: Black-Hole Raytracing
description: Integrating light paths through the curved spacetime produced by a black hole.
img: assets/img/projects/black-hole-raytracing/lensing_grid.png
importance: 1
category: General Relativity
related_publications: false
---

A from-scratch raytracer: one light ray per pixel, launched from a
camera and bent by the geodesic equation of a Schwarzschild or Kerr
black hole until it hits the horizon or escapes.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/photon_paths.png" title="Photon trajectories near the photon sphere" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Rays at a range of impact parameters. Wide enough (blue) whirl past the photon sphere and escape; too tight (red) spiral into the horizon — the dividing line is the shadow's edge.
</div>

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/lensing_grid.png" title="Gravitational lensing of a grid sky" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A checkerboard sky lensed past a Schwarzschild black hole, wound into rings around the shadow — the innermost rings are light that looped the hole before reaching the camera.
</div>

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/schwarzschild_disk.png" title="Lensed accretion disk" class="img-fluid rounded z-depth-1" %}

{% include video.liquid path="assets/video/projects/black-hole-raytracing/disk_flythrough.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A camera swooping toward a thin accretion disk while circling the hole — the photon ring tightens and the lensed far side of the disk swings overhead." %}

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/spin_sweep.png" title="Black-hole shadow vs spin" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The same view at spin a = 0, 0.5, 0.9, 0.998 M. Frame dragging shrinks the shadow and flattens one edge into a D-shape.
</div>

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/gyro_precession.png" title="Geodetic precession of an orbiting gyroscope" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A gyroscope parallel-transported around a circular orbit returns with its spin axis rotated — geodetic precession, matched here to Gravity Probe B's measurement to 0.6%.
</div>

## Code

- [`General_Relativity`](https://github.com/HasanHelal429/General_Relativity) — the Schwarzschild and Kerr raytracers, plus the frame-dragging / Gravity Probe B notebook
