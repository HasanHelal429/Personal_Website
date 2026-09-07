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
tell you the notes; this is the music. Give a wavefunction some initial
shape and momentum and integrate

$$ i\,\frac{\partial \psi}{\partial t} = \Big(-\tfrac12 \nabla^2 + V(\mathbf r)\Big)\psi $$

forward in time. The method here is the **split-step Fourier** (Strang)
scheme — and its whole appeal is that one short piece of code does 1D, 2D
and 3D, and both open and hard-walled boundaries, without changing.

## One step

Split the evolution operator over a small time step into a potential part
and a kinetic part:

{% include figure.liquid path="assets/img/projects/wavepacket-dynamics/split_step_cycle.png" title="One split-step" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The potential is diagonal in real space, so a half-step is an elementwise phase. The kinetic term −½∇² is diagonal in <em>momentum</em> space, so a full step is: Fourier transform, multiply by e<sup>−ik²Δt/2</sup>, transform back. Sandwich them symmetrically and the error per step is O(Δt³), and every step is exactly norm-conserving.
</div>

Against the cases with known answers it behaves: a free Gaussian
wavepacket spreads exactly as $\sigma(t) = \sigma_0\sqrt{1 + (t/2\sigma_0^2)^2}$,
halving $\Delta t$ cuts the error by four (second order), and a stationary
eigenstate propagated through the solver comes out changed by nothing but
the phase $e^{-iEt}$ — the tightest possible check that the kinetic and
potential steps are consistent.

## Tunnelling through a barrier

Send a wavepacket at a rectangular barrier taller than the packet's mean
energy. Classically nothing gets through. Quantum-mechanically a piece
does — it splits, part reflecting, part emerging on the far side.

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/tunneling_1d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A Gaussian packet (E ≈ 1.8) hitting a barrier of height 2.4. The incident and reflected parts interfere into ripples on the left; a smaller transmitted packet appears on the right and moves off. An absorbing layer at each edge stops either piece from wrapping back." %}

Sweeping the incident energy and measuring the transmitted fraction traces
out the tunnelling curve. It sits just below the textbook plane-wave
result, by a growing amount at higher energy — a real, expected systematic:
a wavepacket carries a _spread_ of energies, and near a sharp feature in
$T(E)$ the low-energy tail drags the average down.

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
    Transmission probability versus incident energy: the wavepacket measurement (points) against the exact plane-wave formula (line). They agree to a mean of 0.015 across the sweep, with the wavepacket running low at high energy for the reason above.
</div>

## The double slit

The same solver in two dimensions, aimed at a wall with two gaps in it.
A single incoming wavepacket goes through _both_ slits and interferes with
itself on the far side.

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/double_slit_2d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A wavepacket passing through two slits (wall at the left edge of the frame). The transmitted wave fans out into lobes separated by dark nulls — the two-slit interference pattern, forming in real time." %}

Left running and time-averaged, the far side builds up the full fringe
pattern. The measured spacing of the central fringes matches the
elementary formula $\Delta y \approx \lambda L / d$ — with $\lambda =
2\pi/k_0$ the packet's central de Broglie wavelength — to about 0.3% near
the axis, drifting off further out as the small-angle approximation in
that formula breaks down.

{% include figure.liquid path="assets/img/projects/wavepacket-dynamics/double_slit_pattern.png" title="Two-slit interference pattern" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The time-integrated intensity past the slits. Bright radial lobes with dark fringes between them — the interference pattern the electron double-slit experiment actually measures, here from integrating one wavepacket through the same split-step solver.
</div>

## A bound electron in three dimensions

Nothing about the method changes going to 3D. Put a wavepacket slightly
off-centre in a soft Coulomb well and it orbits — a dipole oscillation of
the probability cloud.

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/bound_electron_3d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A z = 0 slice through a wavepacket launched off-centre in a 3D soft-Coulomb well. The whole cloud swings back and forth through the nucleus." %}

There is no simple closed-form trajectory to check this against, so the
test is energy conservation: the Hamiltonian has no explicit time
dependence, so $\langle H\rangle$ must be constant — and it is, to a few
parts in $10^7$ over the run. The oscillation period independently matches
what the finite-difference eigensolver predicts from the gap between the
two lowest states, to about 1%.

## Running an open problem on a closed grid

The Fourier transform in the kinetic step assumes the domain is periodic —
so a packet heading off one edge reappears at the other, contaminating
everything. Scattering problems (the two above) need that suppressed. The
fix is a **complex absorbing potential**: a smooth imaginary term switched
on in a layer near each edge, so `exp(−iVΔt)` picks up a real decay factor
there and outgoing flux is quietly removed.

{% include video.liquid path="assets/video/projects/wavepacket-dynamics/cap_absorption.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The same packet with and without an absorbing layer. Without it (top) the packet wraps around and comes back. With it (bottom) the outgoing flux is absorbed at the edge — the norm that disappears is exactly the probability that left the box." %}

Tuned reasonably (a smooth ramp a few packet-widths wide), the layer
reflects less than one part in $10^8$ back into the interior, and with it
switched off the propagator conserves norm to machine precision — so any
norm loss is attributable to the layer, not to the method.

## Code

- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) —
  `TDSE_Solver`: the dimension-generic split-step propagator, the FFT/DST
  kinetic step, the potentials and absorbing layer, and the
  finite-difference eigensolver used as an independent check. Validated
  across seven phases (46/46 checks) against free-particle spreading, the
  analytic tunnelling curve, the double-slit formula, and energy
  conservation in 3D.
