---
layout: page
title: Magnetostatics
description: The magnetic field of currents and coils, solved two independent ways and checked against theory, then fed into a relativistic Boris pusher — cyclotron orbits, a magnetic mirror and its loss cone, a magnetic cusp, and a toroidal (tokamak-style) coil ring.
img: assets/img/projects/magnetostatics/toroidal_ring.png
importance: 1
category: Electrodynamics
related_publications: false
---

Static magnetic fields of currents and coils — solved by geometric
multigrid and by direct GPU Biot–Savart — feeding a relativistic Boris
pusher for charged-particle orbits: cyclotron motion, a magnetic
bottle, a cusp, a loss cone, and a tokamak-style coil ring.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/wire_field.png" title="Single wire field" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/solenoid_field.png" title="Solenoid field" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A single infinite wire and a solenoid cross-section, both solved on the 2D multigrid — matching their analytic fields to well under a few percent.
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
    A current loop and a Helmholtz pair, solved by GPU Biot–Savart — the Helmholtz "sweet spot" between the coils is visibly uniform.
</div>

{% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_bottle_field.png" title="Magnetic bottle field" class="img-fluid rounded z-depth-1" %}

{% include video.liquid path="assets/video/projects/magnetostatics/magnetic_bottle.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Two coils carrying current the same way make a magnetic bottle: a charged particle spirals toward one throat, reflects, and bounces back — the mechanism behind the Van Allen belts and mirror-machine fusion devices." %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/magnetostatics/cyclotron.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/magnetostatics/exb_drift.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: a single charge's cyclotron orbit in a uniform field. Right: two opposite charges gyrating in opposite senses in crossed E and B fields still drift together — E×B drift is sign-independent.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_bottle_field.png" title="Bottle: confines" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/magnetostatics/magnetic_cusp.png" title="Cusp: doesn't" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The same two coils, one current sign flipped. Left (mirror): the particle bounces indefinitely. Right (cusp): the field lines meet at a saddle instead, and the particle escapes through the on-axis null on its first approach.
</div>

{% include figure.liquid path="assets/img/projects/magnetostatics/loss_cone.png" title="Loss cone: three pitch angles" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Three charges, same bottle field, different pitch angles: comfortably confined (orange), just past the critical angle (green), well inside the loss cone (blue) — the same mechanism that empties Earth's radiation belts into the atmosphere near the poles.
</div>

{% include figure.liquid path="assets/img/projects/magnetostatics/toroidal_ring.png" title="Toroidal coil ring" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Eight loop coils arranged in a ring, tokamak-style — the field threads through the ring in a closed toroidal band.
</div>

## Code

- OpenGL Physics (`09_magnetostatics`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
