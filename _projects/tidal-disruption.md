---
layout: page
title: Tidal Disruption of a Star
description: A self-gravitating SPH star, built from a Lane-Emden polytrope, torn apart by a black hole's tidal field.
img: assets/img/projects/tidal-disruption/tidal_disruption_beta3_thumb.png
importance: 3
category: General Relativity
related_publications: false
chart:
  plotly: true
---

A star is built from scratch as an SPH particle system — a few thousand
self-gravitating particles sampled from a Lane-Emden polytrope, relaxed
to hydrostatic equilibrium — then thrown at a black hole on a range of
orbits to see how it comes apart.

{% include figure.liquid path="assets/img/projects/tidal-disruption/encounter_schematic.png" title="Tidal-encounter geometry" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    beta = r_t / r_p: the tidal radius over how close the orbit actually brings the star to the hole. Bigger beta, deeper plunge.
</div>

{% include video.liquid path="assets/video/projects/tidal-disruption/tidal_disruption_beta3.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Deep plunge, beta=3: the star stretches into a tidal debris stream within one pass. Colored by density." %}

{% include video.liquid path="assets/video/projects/tidal-disruption/fallback_return.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Same encounter, run 50x longer: about half the star stays bound and falls back past the black hole." %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/tidal-disruption/newtonian_encounter.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/tidal-disruption/pw_encounter.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Same star, same orbit — Newtonian point mass (left) vs. a Paczynski-Wiita black hole (right). The stronger effective gravity pulls the debris measurably tighter before it escapes.
</div>

{% include video.liquid path="assets/video/projects/tidal-disruption/binary_feeding.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Bound eccentric orbit (beta=0.5, e=0.6) around a black hole only 10x the star's mass: repeated partial stripping over ~4.3 orbits instead of one-shot disruption." %}

{% include video.liquid path="assets/video/projects/tidal-disruption/circular_feeding_3d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="Circular orbit just inside the disruption threshold (beta=0.47): a slow trickle of mass each orbit shrinks the star's Roche lobe until the stripping runs away into full breakup." %}

All 4000 particles above are real 3D positions — here's the same run as
an interactive point cloud. Drag to rotate, scroll to zoom, or press Play:

<div id="tidal-3d-scatter" style="width:100%;height:600px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__tidal3dLoaded) {
    window.__tidal3dLoaded = true;
    fetch("{{ '/assets/json/projects/tidal-disruption/circular_feeding_3d.json' | relative_url }}")
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

- OpenGL Physics (`06_tidal_disruption`) — the SPH star, its Lane-Emden
  initial conditions, and the black-hole encounter machinery — not yet
  public
