---
layout: page
title: Relativistic Hydrodynamics
description: Evolving fluid on a fixed black-hole background with a shock-capturing GRHD solver — accretion tori, tidal disruption of stars, and a runaway circular-orbit feeding instability.
img: assets/video/projects/relativistic-hydrodynamics/kerr_torus.webm
importance: 2
category: General Relativity
related_publications: false
chart:
  plotly: true
---

The other half of the general-relativity work: instead of tracing light
through a fixed spacetime, evolve _matter_ on one. A conservative,
shock-capturing finite-volume solver advances relativistic fluid on a
fixed Kerr background; the same framework, in a cheap Newtonian limit,
follows a star being tidally shredded by a black hole. Part of the
C++/OpenGL simulation framework built for real-time, GPU-friendly
physics.

## Accretion tori on a Kerr background

A general-relativistic hydrodynamics solver evolves fluid on a fixed
Kerr background using a conservative, shock-capturing finite-volume
scheme. Below, a Fishbone-Moncrief torus settles into a steady
accretion flow around a spinning (a=0.9M) black hole.

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/kerr_torus.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A relativistic accretion torus around a Kerr black hole (a=0.9M), evolved with a shock-capturing GRHD solver." %}

## Tidal disruption of a star

The same simulation framework, run in a Newtonian limit with a
Paczynski-Wiita pseudo-potential to approximate strong-field gravity
cheaply, models a star being tidally shredded on a close encounter with
a black hole (impact parameter beta=3):

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/tidal_disruption_beta3.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star disrupted into a tidal debris stream on a close, deeply-plunging encounter (beta=3)." %}

Beta=3 is a single deep plunge; a star on a circular orbit tells a
different story. Placed just inside its own tidal-disruption threshold
(beta=0.47, barely above the point where nothing strips at all), it
sheds a slow trickle each pass rather than shredding in one encounter —
until, after enough orbits, the stripping itself grows the star's
effective Roche lobe faster than the star can hold on, and the slow
trickle runs away into a full breakup. All 4000 SPH particles are real
3D positions here, not a 2D projection:

{% include video.liquid path="assets/video/projects/relativistic-hydrodynamics/circular_feeding_3d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star on a circular orbit (beta=0.47) sheds a slow trickle for several orbits before the stripping runs away into a full tidal breakup, spun into a debris stream trailing the black hole. Colored by density; camera recentered on the star's own (density-weighted) center of mass." %}

The same run, as an actual interactive 3D point cloud — drag to rotate,
scroll to zoom, or press Play to watch the full ~12-orbit breakup
unfold from any angle:

<div id="tidal-3d-scatter" style="width:100%;height:600px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__tidal3dLoaded) {
    window.__tidal3dLoaded = true;
    fetch("{{ '/assets/json/projects/relativistic-hydrodynamics/circular_feeding_3d.json' | relative_url }}")
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

- OpenGL Physics (the GRHD and tidal-disruption solvers) — not yet public
