---
layout: page
title: General Relativity
description: Raytracing through curved spacetime and relativistic hydrodynamics — light bending, black hole shadows, accretion, and tidal disruption.
img: assets/img/projects/general-relativity/schwarzschild_render_with_disk.png
importance: 1
category: General Relativity
related_publications: false
chart:
  plotly: true
---

General relativity work here comes at the same physics from two
angles: raytracing null geodesics through curved spacetime to render
what a black hole actually looks like, and evolving relativistic fluid
on a fixed black-hole background to simulate accretion and tidal
disruption. The raytracers are Python; the hydrodynamics solver is a
C++/OpenGL code from a shared simulation framework built for
real-time, GPU-friendly physics.

## Light bending & black hole shadows

A raytracer through the Schwarzschild spacetime of a static black hole
bends camera rays along null geodesics instead of straight lines,
rendering gravitational lensing, the photon sphere, and a lensed
accretion disk.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/general-relativity/schwarzschild_render_with_disk.png" title="Schwarzschild lensed accretion disk" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/general-relativity/schwarzschild_flythrough.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: the lensed accretion disk seen from further back, showing the full disk wrapping around the shadow with a secondary lensed image arcing above it. Right: a swooping camera trajectory — varying both distance and viewing angle, rather than a fixed orbit — reveals how the photon ring and disk lensing change as the camera moves.
</div>

Extending the same raytracer to a spinning (Kerr) black hole adds
frame dragging: the black hole's shadow shrinks and shifts off-center
as spin increases, and a disk co-rotating with the hole (prograde) can
orbit much closer in than one orbiting against it (retrograde), since
frame dragging moves the innermost stable orbit itself.

{% include figure.liquid path="assets/img/projects/general-relativity/kerr_hero.png" title="Kerr lensed accretion disk" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The lensed disk around a spinning black hole (a=0.9M) — frame dragging skews the shadow and lensed image away from the mirror symmetry of the non-spinning case above.
</div>

{% include video.liquid path="assets/video/projects/general-relativity/kerr_spinsweep.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The shadow continuously shrinking and shifting as spin a sweeps from 0 to near-extremal 0.998 and back." %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/general-relativity/kerr_prograde.png" title="Prograde accretion disk" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/general-relativity/kerr_retrograde.png" title="Retrograde accretion disk" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    At a=0.9: the prograde disk's inner edge (r_isco=2.32M) sits much closer to the hole than the retrograde disk's (r_isco=8.72M), since frame dragging moves the innermost stable orbit itself.
</div>

## Relativistic hydrodynamics & tidal disruption

A general-relativistic hydrodynamics solver evolves fluid on a fixed
Kerr background using a conservative, shock-capturing finite-volume
scheme. Below, a Fishbone-Moncrief torus settles into a steady
accretion flow around a spinning (a=0.9M) black hole.

{% include video.liquid path="assets/video/projects/general-relativity/kerr_torus.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A relativistic accretion torus around a Kerr black hole (a=0.9M), evolved with a shock-capturing GRHD solver." %}

The same simulation framework, run in a Newtonian limit with a
Paczynski-Wiita pseudo-potential to approximate strong-field gravity
cheaply, models a star being tidally shredded on a close encounter
with a black hole (impact parameter beta=3):

{% include video.liquid path="assets/video/projects/general-relativity/tidal_disruption_beta3.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star disrupted into a tidal debris stream on a close, deeply-plunging encounter (beta=3)." %}

Beta=3 is a single deep plunge; a star on a circular orbit tells a
different story. Placed just inside its own tidal-disruption threshold
(beta=0.47, barely above the point where nothing strips at all), it
sheds a slow trickle each pass rather than shredding in one encounter
— until, after enough orbits, the stripping itself grows the star's
effective Roche lobe faster than the star can hold on, and the slow
trickle runs away into a full breakup. All 4000 SPH particles are
real 3D positions here, not a 2D projection:

{% include video.liquid path="assets/video/projects/general-relativity/circular_feeding_3d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star on a circular orbit (beta=0.47) sheds a slow trickle for several orbits before the stripping runs away into a full tidal breakup, spun into a debris stream trailing the black hole. Colored by density; camera recentered on the star's own (density-weighted) center of mass." %}

The same run, as an actual interactive 3D point cloud — drag to
rotate, scroll to zoom, or press Play to watch the full ~12-orbit
breakup unfold from any angle:

<div id="tidal-3d-scatter" style="width:100%;height:600px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__tidal3dLoaded) {
    window.__tidal3dLoaded = true;
    fetch("{{ '/assets/json/projects/general-relativity/circular_feeding_3d.json' | relative_url }}")
      .then(r => r.json())
      .then(fig => {
        Plotly.newPlot('tidal-3d-scatter', fig.data, fig.layout).then(() => {
          Plotly.addFrames('tidal-3d-scatter', fig.frames);
        });
      });
  }
});
</script>

## Code

- [`General_Relativity`](https://github.com/HasanHelal429/General_Relativity) — Schwarzschild/Kerr raytracers
- OpenGL Physics (relativistic hydrodynamics solver) — not yet public
