---
layout: page
title: Radiation from Moving Charges
description: The exact Liénard–Wiechert fields of accelerating point charges on the GPU — dipole radiation, array factors, relativistic beaming, a scattering encounter, and a self-consistent radiative inspiral where charges move under each other's retarded fields.
img: assets/img/projects/radiation-moving-charges/synchrotron.png
importance: 3
category: Electrodynamics
related_publications: false
chart:
  plotly: true
---

The exact Liénard–Wiechert field of a point charge on an arbitrary
worldline, evaluated on a GPU grid — dipole and synchrotron radiation,
array interference, relativistic beaming, and a self-consistent
many-body solver where the charges move under each other's own
retarded fields.

{% include video.liquid path="assets/video/projects/radiation-moving-charges/uniform_motion.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A charge moving at constant velocity (beta=0.9) — no acceleration, so no radiation, but the field is squashed into a transverse pancake by relativistic length contraction, validated against the closed-form solution to 5e-16." %}

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/radiation-moving-charges/oscillating_dipole.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/radiation-moving-charges/synchrotron.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: an oscillating charge radiating the classic dipole doughnut. Right: a charge on a circular orbit at gamma=5 — the radiation collimates into a forward searchlight that sweeps around like a synchrotron beam.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/radiation-moving-charges/dipole_array.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/radiation-moving-charges/dipole_array_pattern.png" title="Far-field array pattern" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Two in-phase oscillators interfering (left) and the measured far-field pattern (right) — the single-element doughnut split into a four-lobed clover by the array factor. The measured beaming half-angle scales as 1/gamma and the radiated power as gamma^4, matching theory to four significant figures.
</div>

{% include video.liquid path="assets/video/projects/radiation-moving-charges/figure8.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A charge on a Lissajous figure-8 path, radiating a superposition of two frequencies at once instead of a single tone." %}

{% include video.liquid path="assets/video/projects/radiation-moving-charges/two_body_inspiral.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A bound pair of opposite charges losing energy to radiation and spiralling together over ~3 orbits — the electromagnetic analog of a gravitational-wave binary." %}

The same run as an interactive figure — trajectories on the left, the energy budget on the right, scrub or play through the inspiral:

<div id="ed-inspiral" style="width:100%;height:540px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__edInspiralLoaded) {
    window.__edInspiralLoaded = true;
    fetch("{{ '/assets/json/projects/radiation-moving-charges/inspiral.json' | relative_url }}")
      .then((r) => r.json())
      .then((fig) => {
        Plotly.newPlot("ed-inspiral", fig.data, fig.layout, { responsive: true }).then(() =>
          Plotly.addFrames("ed-inspiral", fig.frames),
        );
      });
  }
});
</script>

{% include video.liquid path="assets/video/projects/radiation-moving-charges/coulomb_bremsstrahlung.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A light charge deflecting past a fixed heavy one instead of orbiting it — the same self-consistent solver in its unbound, scattering regime. Sweeping the impact parameter recovers the classic b^-3 bremsstrahlung scaling." %}

## Code

- OpenGL Physics (`11_retarded_fields`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
