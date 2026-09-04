---
layout: page
title: Black-Hole Raytracing
description: Integrating null geodesics backwards from every pixel — gravitational lensing, black-hole shadows, lensed accretion disks, and the deformed shadow and gyroscope precession of a spinning black hole.
img: assets/img/projects/black-hole-raytracing/lensing_grid.png
importance: 1
category: General Relativity
related_publications: false
---

Rendering a black hole means giving up straight lines. Light follows null
geodesics of the curved metric, so a pixel's colour is set not by looking
along a ray but by integrating one _backwards_ — from the camera, through
the warped spacetime, until it either escapes to the sky, lands on the
accretion disk, or crosses the horizon. These are two such raytracers, one
for the static Schwarzschild black hole and one for the spinning Kerr
black hole, written in Python.

## Rendering a black hole backwards

Every pixel gets one ray, launched from the camera in the pixel's
direction and integrated against the geodesic equation until it resolves.
The trick that keeps this tractable is symmetry. **Schwarzschild** is
spherically symmetric, so every photon's orbit stays in a single plane
through the centre — the full 4D geodesic equation collapses to one
clean 2D orbit ODE in $u = 1/r$,

$$
\frac{d^2 u}{d\varphi^2} = 3 M u^2 - u ,
$$

integrated per ray and rotated back into 3D. **Kerr** is only
_axisymmetric_: frame dragging pulls photons out of any fixed plane, so
that reduction is gone. Instead each photon carries a third conserved
quantity — the Carter constant — which separates the radial and polar
motion into two independent 1D problems once you switch to Mino time. At
zero spin the Kerr integrator reproduces the Schwarzschild one
trajectory for trajectory.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/photon_paths.png" title="Photon trajectories near the photon sphere" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Photon paths fired inward from a camera (yellow dot) at a range of impact parameters. Rays aimed wide enough (blue) whirl once past the unstable photon sphere at r = 3M (dashed) and escape; rays aimed too tight (red) spiral into the horizon. The dividing line is the critical impact parameter b = 3√3 M ≈ 5.2M — the edge of the shadow.
</div>

## Gravitational lensing and the shadow

Point the camera at a black hole against a textured sky and the lensing
is direct to see: the background is stretched into arcs, the region right
behind the hole is imaged into a bright ring, and the same patch of sky
appears again and again in ever-thinner copies approaching the shadow
edge. The shadow itself is the set of directions whose rays end on the
horizon — its angular radius is fixed by the photon sphere, and the
rendered size matches the analytic prediction
$\arcsin\!\big(b_\text{crit}\sqrt{f(r_\text{cam})}/r_\text{cam}\big)$ to
within one pixel.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/lensing_grid.png" title="Gravitational lensing of a grid sky" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A checkerboard celestial sphere (hue set by longitude) seen past a Schwarzschild black hole. The flat grid is wound into concentric rings around the shadow; the innermost visible rings are higher-order images — light that looped one or more times around the hole before reaching the camera.
</div>

## Accretion disks

Adding a thin equatorial disk — inner edge at the innermost stable
circular orbit ($r_\text{isco} = 6M$ for Schwarzschild), brightness from a
non-relativistic $T(r) \propto r^{-3/4}$ profile — produces the image
that made black holes famous on film. The disk's far side, behind the
hole, is lensed up and over the shadow into an arc; its underside is
lensed into a second arc below. The camera never moves through the disk
plane, but the light does.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/schwarzschild_disk.png" title="Lensed accretion disk" class="img-fluid rounded z-depth-1" %}

{% include video.liquid path="assets/video/projects/black-hole-raytracing/disk_flythrough.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A camera swooping in toward the disk plane while circling the hole — the photon ring tightens and the lensed far side of the disk swings overhead as the viewing angle flattens. Disk colour is temperature only; relativistic Doppler beaming (which would brighten the approaching side) is not modelled here." %}

## Spin: the deformed shadow

A spinning black hole drags the spacetime around with it, and the photon
orbits go with it. Co-rotating (prograde) light can hold a circular orbit
much closer in than counter-rotating light, so the photon sphere splits
in two and the shadow stops being a circle: as spin increases it shrinks
_and_ flattens on the side where the hole is rotating toward the camera,
into a characteristic D-shape. Every Kerr metric quantity in the code —
horizon, ergosphere, photon-sphere and ISCO radii — reduces exactly to
its Schwarzschild value as the spin goes to zero.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/spin_sweep.png" title="Black-hole shadow vs spin" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The same equatorial view at spin a = 0, 0.5, 0.9, 0.998 (in units of M). The shadow shrinks and one edge straightens as frame dragging tightens the prograde photon orbit.
</div>

## Frame dragging & Gravity Probe B

A spinning black hole reaches beyond the light it bends. Frame dragging
pulls the innermost stable circular orbit inward for anything co-rotating
with the hole — at spin $a = 0.9$ from the Schwarzschild $6M$ down to
$2.3M$ — and outward to $8.7M$ for a counter-rotating orbit. And a
gyroscope carried around any orbit in curved spacetime comes back with
its spin axis turned.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/gyro_precession.png" title="Geodetic precession of an orbiting gyroscope" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A gyroscope parallel-transported once around a circular orbit comes back with its spin axis rotated — geodetic precession. The per-orbit angle is exaggerated here; for a real orbit it is a slow drift.
</div>

Gravity Probe B measured exactly this for a set of gyroscopes in Earth
orbit — a large **geodetic** precession from the curvature of spacetime,
plus a tiny **frame-dragging** term from Earth's own rotation.
Parallel-transporting a spin vector around a circular orbit in this code,
with Earth's actual mass and the satellite's orbit, reproduces the
geodetic term to **0.6%** — 6.64 arcseconds per year against the
mission's 6.6.

## Code

- [`General_Relativity`](https://github.com/HasanHelal429/General_Relativity) — the Schwarzschild and Kerr raytracers, plus the frame-dragging / Gravity Probe B notebook
