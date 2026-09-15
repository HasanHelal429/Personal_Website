---
layout: page
title: Wavepacket Dynamics
description: "The time-dependent Schrödinger equation by split-step Fourier — one dimension-generic solver watching wavefunctions actually move: a packet tunnelling through a barrier it shouldn't clear, interference building at a double slit, a bound electron sloshing in 3D, and how you run an open scattering problem on a finite grid."
img: assets/img/projects/wavepacket-dynamics/thumb.png
importance: 2
category: Quantum Mechanics
related_publications: false
chart:
  plotly: true
---

The [stationary states]({{ '/projects/stationary-states/' | relative_url }})
give the notes; this is the music. A dimension-generic split-step
Fourier solver — the same code runs 1D, 2D, and 3D — watching
wavefunctions actually move: tunnelling, double-slit interference, a
bound electron in 3D, and an open scattering problem on a finite grid.

{% include figure.liquid path="assets/img/projects/wavepacket-dynamics/split_step_cycle.png" title="One split-step" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    One step: a real-space phase kick from the potential, a momentum-space phase kick from the kinetic term, sandwiched symmetrically for second-order accuracy and exact norm conservation.
</div>

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/tunneling_1d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A Gaussian packet hitting a barrier taller than its mean energy. Classically nothing gets through; quantum-mechanically part reflects and part tunnels through to the far side." %}

<div id="wd-transmission" style="width:100%;height:430px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__wdTransLoaded) {
    window.__wdTransLoaded = true;
    fetch("{{ '/assets/json/projects/wavepacket-dynamics/transmission_vs_energy.json' | relative_url }}")
      .then((r) => r.json())
      .then((fig) => Plotly.newPlot("wd-transmission", fig.data, fig.layout, { responsive: true }));
  }
});
</script>

<div class="caption">
    Measured transmission probability vs. incident energy (points) against the exact plane-wave formula (line) — agreeing to a mean of 0.015 across the sweep.
</div>

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/double_slit_2d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The same solver in two dimensions: a single wavepacket goes through both slits at once and interferes with itself on the far side." %}

{% include figure.liquid path="assets/img/projects/wavepacket-dynamics/double_slit_pattern.png" title="Two-slit interference pattern" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The time-integrated intensity past the slits — bright lobes with dark fringes between them, the pattern the electron double-slit experiment actually measures. The measured fringe spacing matches the elementary formula to about 0.3% near the axis.
</div>

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/bound_electron_3d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A wavepacket launched off-centre in a 3D soft-Coulomb well orbits the nucleus, a dipole oscillation of the probability cloud — energy conserved to a few parts in 10^7 over the run." %}

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/cap_absorption.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The Fourier method assumes a periodic domain, so without help a packet leaving one edge reappears at the other (top). A complex absorbing layer near each edge removes outgoing flux instead (bottom)." %}

## Code

- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) —
  `TDSE_Solver`: the dimension-generic split-step propagator, the FFT/DST
  kinetic step, the potentials and absorbing layer, and the
  finite-difference eigensolver used as an independent check. Validated
  across seven phases (46/46 checks) against free-particle spreading, the
  analytic tunnelling curve, the double-slit formula, and energy
  conservation in 3D.
