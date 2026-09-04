---
layout: page
title: Black-Hole Raytracing
description: Tracing null geodesics through Schwarzschild and Kerr spacetime to render gravitational lensing, black-hole shadows, photon rings, and lensed accretion disks.
img: assets/img/projects/black-hole-raytracing/schwarzschild_render_with_disk.png
importance: 1
category: General Relativity
related_publications: false
---

A camera in flat space shoots straight rays. A camera near a black hole
doesn't: light follows null geodesics of the curved metric, so rendering
what a black hole actually looks like means integrating the geodesic
equation backwards from every pixel. These raytracers do that for the
Schwarzschild (static) and Kerr (spinning) spacetimes, in Python.

## Light bending & black-hole shadows

A raytracer through the Schwarzschild spacetime of a static black hole
bends camera rays along null geodesics instead of straight lines,
rendering gravitational lensing, the photon sphere, and a lensed
accretion disk.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/black-hole-raytracing/schwarzschild_render_with_disk.png" title="Schwarzschild lensed accretion disk" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/black-hole-raytracing/schwarzschild_flythrough.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: the lensed accretion disk seen from further back, showing the full disk wrapping around the shadow with a secondary lensed image arcing above it. Right: a swooping camera trajectory — varying both distance and viewing angle, rather than a fixed orbit — reveals how the photon ring and disk lensing change as the camera moves.
</div>

## Frame dragging around a spinning black hole

Extending the same raytracer to a spinning (Kerr) black hole adds frame
dragging: the black hole's shadow shrinks and shifts off-center as spin
increases, and a disk co-rotating with the hole (prograde) can orbit
much closer in than one orbiting against it (retrograde), since frame
dragging moves the innermost stable orbit itself.

{% include figure.liquid path="assets/img/projects/black-hole-raytracing/kerr_hero.png" title="Kerr lensed accretion disk" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The lensed disk around a spinning black hole (a=0.9M) — frame dragging skews the shadow and lensed image away from the mirror symmetry of the non-spinning case above.
</div>

{% include video.liquid path="assets/video/projects/black-hole-raytracing/kerr_spinsweep.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The shadow continuously shrinking and shifting as spin a sweeps from 0 to near-extremal 0.998 and back." %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/black-hole-raytracing/kerr_prograde.png" title="Prograde accretion disk" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/black-hole-raytracing/kerr_retrograde.png" title="Retrograde accretion disk" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    At a=0.9: the prograde disk's inner edge (r_isco=2.32M) sits much closer to the hole than the retrograde disk's (r_isco=8.72M), since frame dragging moves the innermost stable orbit itself.
</div>

## Code

- [`General_Relativity`](https://github.com/HasanHelal429/General_Relativity) — the Schwarzschild and Kerr raytracers
