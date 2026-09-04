---
layout: page
title: Radiation from Moving Charges
description: The exact Liénard–Wiechert fields of accelerating point charges on the GPU — dipole radiation, array factors, relativistic beaming, and a self-consistent radiative inspiral where charges move under each other's retarded fields.
img: assets/img/projects/radiation-moving-charges/synchrotron.png
importance: 3
category: Electrodynamics
related_publications: false
chart:
  plotly: true
---

The exact solution of Maxwell's equations for a point source on an
arbitrary worldline — the Liénard–Wiechert potentials — taken in two
directions the textbook treatment stops short of: evaluated on a GPU
grid for interactivity, and then closed into a self-consistent
many-body problem where the charges move under each other's _retarded_
fields. The third electrodynamics solver on the C++/OpenGL framework.

## The Liénard–Wiechert field

The exact field of a point charge on an arbitrary worldline, evaluated
by solving the retarded-time equation $c(t - t_{\text{ret}}) =
|\mathbf{x} - \mathbf{r}(t_{\text{ret}})|$ per grid cell on the GPU and
applying the velocity (near) + acceleration (radiation) split:

$$
\mathbf{E} = \frac{q}{4\pi\varepsilon_0}\left[
\frac{(\mathbf{n}-\boldsymbol\beta)(1-\beta^2)}{\kappa^3 R^2}
+ \frac{\mathbf{n}\times\big((\mathbf{n}-\boldsymbol\beta)\times\dot{\boldsymbol\beta}\big)}{c\,\kappa^3 R}
\right],\quad \kappa = 1 - \mathbf{n}\cdot\boldsymbol\beta.
$$

For a charge oscillating along a line this gives the dipole radiation
doughnut; a charge on a circular orbit at $\gamma = 5$ throws its
radiation into a forward searchlight that sweeps around with it — the
synchrotron beam, compressed into a half-angle $\sim 1/\gamma$.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/radiation-moving-charges/oscillating_dipole.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/radiation-moving-charges/synchrotron.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    Left: an oscillating charge radiating the dipole pattern — nulls along the oscillation axis, maxima broadside, a slight forward tip from the finite velocity. Right: a charge on a circular orbit at β = 0.98 (γ = 5). The radiation collimates into a narrow forward beam that sweeps around like a lighthouse, tracing an Archimedean spiral in the field — this is how a synchrotron light source works.
</div>

## Interference and relativistic beaming

Two oscillators side by side interfere, and the far-field pattern is the
single-element doughnut times the array factor — a broadside main lobe
with the side lobes set by the element spacing.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/radiation-moving-charges/dipole_array.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/radiation-moving-charges/dipole_array_pattern.png" title="Array factor" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: two in-phase oscillators a half-wavelength apart, their wavefronts interfering. Right: the radiation pattern measured a couple of wavelengths out (blue) against the analytic element-pattern × array-factor prediction (green).
</div>

The measured beaming half-angle scales as $1/\gamma$ and the total
radiated power as $\gamma^4$:

{% include figure.liquid path="assets/img/projects/radiation-moving-charges/beaming.png" title="Relativistic beaming" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Forward-lobe half-angle ∝ 1/γ (fit slope −1.05) and total radiated power ∝ γ⁴ (fit slope 4.00 to four significant figures), from the exact retarded-quantity angular distribution. The 2D field grid can't resolve the sub-cell synchrotron pulse for a direct measurement — this comes from evaluating the same Liénard–Wiechert expression at infinity.
</div>

## Self-consistent radiative inspiral

Letting the charges move under each other's _retarded_ fields — a
trajectory ring buffer feeds the retarded-time solve, a relativistic
pusher integrates the motion — makes radiative inspiral emerge from the
dynamics. A bound pair of opposite charges loses energy to radiation and
spirals in, exactly the way a gravitational-wave binary does.

{% include figure.liquid path="assets/img/projects/radiation-moving-charges/two_body_inspiral.png" title="Two-body inspiral" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Left: the two charges spiralling together over ~3 orbits. Middle: the measured separation (black) tracks the analytic two-body dipole decay law (red dashed) — with the radiated power halved, because the pairwise retarded interaction without the Abraham–Lorentz self-force carries exactly half of a symmetric pair's radiation reaction. Right: the energy budget — kinetic + interaction PE + integrated radiated power — closes to under 1% of the total energy swing over the run.
</div>

A companion parameter study sweeps the initial separation and recovers
the $-\dot E \propto d^{-4}$ scaling to a fit slope of $-3.96$, with the
half-power factor holding constant across the sweep.

The same run as an interactive figure — the trajectories on the left,
the energy budget on the right, scrub or play through the inspiral:

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

## Code

- OpenGL Physics (`11_retarded_fields`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
