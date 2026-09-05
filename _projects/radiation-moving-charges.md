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

A charge with no acceleration ($\dot{\boldsymbol\beta}=0$) is the
simplest non-trivial case: only the velocity term survives, and even
though nothing is radiating, the field itself isn't the ordinary
isotropic Coulomb field once the charge is moving fast. It's squashed
into a transverse pancake — stronger in the plane perpendicular to the
motion, weaker fore and aft — the field-line manifestation of
relativistic length contraction.

{% include video.liquid path="assets/video/projects/radiation-moving-charges/uniform_motion.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A charge moving at constant velocity (beta=0.9, gamma~2.29, no acceleration so no radiation) — the dot marks the charge itself, with its trail behind it. The field concentrates in the plane transverse to the motion instead of spreading isotropically, and translates rigidly with the charge — validated against the closed-form solution to 5e-16." %}

Give the same charge an acceleration and it radiates. For a charge
oscillating along a line this gives the dipole radiation doughnut; a
charge on a circular orbit at $\gamma = 5$ throws its radiation into a
forward searchlight that sweeps around with it — the synchrotron beam,
compressed into a half-angle $\sim 1/\gamma$.

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
        {% include figure.liquid path="assets/img/projects/radiation-moving-charges/dipole_array_pattern.png" title="Far-field array pattern" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: two in-phase oscillators a half-wavelength apart, their wavefronts interfering (the dots mark the charges, their short trails the oscillation range). Right: the array's actual far-field pattern — |E| sampled on a circle several wavelengths out and time-averaged to drop the fill-in transient, the same measurement docs/SIMULATION.md validates against the analytic element-pattern × array-factor prediction. The four-lobed clover shape is that product: the single-element doughnut split into quarters by this spacing's array factor.
</div>

The measured beaming half-angle scales as $1/\gamma$ (fit slope
$-1.05$) and the total radiated power as $\gamma^4$ (fit slope $4.00$
to four significant figures) — from the exact retarded-quantity angular
distribution, evaluated at infinity since the 2D field grid can't
resolve the sub-cell synchrotron pulse directly.

## A more complex source: the figure-8 radiator

Every source so far moves at a single frequency. A charge on a
figure-8 (Lissajous) path — oscillating across the pattern at $\omega$
and along it at $2\omega$ — radiates a superposition of both instead of
one pure tone. Seen close in it still just looks like a spiral; wide
enough out to hold several wavelengths in frame, the two frequencies'
slightly different pitch is there in the field, even if it takes a
careful look to see it over the dominant fundamental:

{% include video.liquid path="assets/video/projects/radiation-moving-charges/figure8.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A charge on a Lissajous figure-8 path, radiating both its fundamental and first-harmonic frequency at once instead of the single tone every other source on this page emits. The trail traces the actual figure-8 the charge is following." %}

## Self-consistent radiative inspiral

Letting the charges move under each other's _retarded_ fields — a
trajectory ring buffer feeds the retarded-time solve, a relativistic
pusher integrates the motion — makes radiative inspiral emerge from the
dynamics. A bound pair of opposite charges loses energy to radiation and
spirals in, exactly the way a gravitational-wave binary does. The
measured separation tracks the analytic two-body dipole decay law, with
the radiated power exactly half what a symmetric pair's full
self-consistent radiation reaction would give — the pairwise retarded
interaction here has no Abraham–Lorentz self-force term, so it only
ever carries half the story. The energy budget (kinetic + interaction
potential + integrated radiated power) closes to under 1% of the total
energy swing over the run, and a companion parameter sweep over the
initial separation recovers the $-\dot{E} \propto d^{-4}$ scaling to a
fit slope of $-3.96$, with the half-power factor holding constant
across the sweep.

{% include video.liquid path="assets/video/projects/radiation-moving-charges/two_body_inspiral.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The two charges (orange +q, blue -q) spiralling together over ~3 orbits, each with its full trajectory traced behind it — the slow inward drift of the ring is the radiative decay itself." %}

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

## A scattering encounter: Coulomb bremsstrahlung

A bound orbit isn't the only thing the self-consistent solver can do.
Send a light charge past a fixed heavy one on a straight-line approach
instead of a closed orbit, and it deflects — a scattering encounter
rather than an inspiral, radiating a burst as it goes rather than a
steady drain. Sweeping the impact parameter and measuring the radiated
energy recovers the classic $b^{-3}$ bremsstrahlung scaling.

{% include video.liquid path="assets/video/projects/radiation-moving-charges/coulomb_bremsstrahlung.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A light charge (right) deflects past a fixed heavy charge (left) instead of orbiting it — the same self-consistent solver as the inspiral above, in its unbound regime. The trail traces the actual scattering curve, bending visibly at closest approach; the radiated burst it leaves behind is real but faint at this scale." %}

## Code

- OpenGL Physics (`11_retarded_fields`) — not yet public
- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) — the Python prototype this was ported from
