---
layout: page
title: Stationary States of the Schrödinger Equation
description: "How quantum mechanics actually starts — solving the time-independent Schrödinger equation for the energy eigenstates. The three problems with exact answers, then everything else by putting the Hamiltonian on a grid and diagonalizing — superpositions and revivals, tunnelling splittings, quasi-bound decay, and how a spectrum shifts in a field."
img: assets/img/projects/stationary-states/thumb.png
importance: 1
category: Quantum Mechanics
related_publications: false
chart:
  plotly: true
---

The energy eigenstates of the Schrödinger equation: the three
exactly-solvable problems in closed form, a finite-difference
eigensolver for everything else, and time evolution built from
superpositions of the results — revivals, tunnelling splittings,
quasi-bound decay, and a spectrum shifting in an electric field.

{% include figure.liquid path="assets/img/projects/stationary-states/three_models.png" title="Three exactly-solvable potentials and their energy ladders" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The particle in a box, the harmonic oscillator, and the hydrogen atom — the three potentials whose eigenvalue problem has a closed-form answer.
</div>

{% include figure.liquid path="assets/img/projects/stationary-states/box_2d_eigenstates.png" title="Eigenstates of a two-dimensional box" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The lowest six eigenstates of a 2D box. (2,1) and (1,2) are degenerate, as are (3,1) and (1,3) — a degeneracy from the square's symmetry alone.
</div>

{% include figure.liquid path="assets/img/projects/stationary-states/hydrogen_orbitals.png" title="Hydrogen orbitals" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The 1s, 2p<sub>z</sub>, and 3d<sub>z²</sub> hydrogen orbitals, built from the analytic radial functions and real spherical harmonics — the shapes chemistry inherits.
</div>

{% include video.liquid path="assets/video/projects/stationary-states/wavepacket_in_a_box.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A Gaussian wavepacket in a box, reconstructed from 60 eigenstates. It bounces, disperses into an interference carpet, and revives — a quantum revival driven entirely by the box's E_n ~ n^2 spectrum." %}

{% include video.liquid path="assets/video/projects/stationary-states/coherent_state.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A coherent state of the harmonic oscillator rides the exact classical trajectory rigidly, with no spreading at all — the closest a quantum state gets to a classical particle." %}

### Build your own superposition

Set the weight of each box eigenstate and watch $|\psi(x,t)|^2$ evolve.

<div class="row justify-content-center">
  <div class="col-lg-10">
    <div id="ss-superpose" style="width:100%;height:340px;"></div>
    <div id="ss-controls" style="margin-top:0.6rem;font-size:0.9rem;"></div>
  </div>
</div>

<script>
(function () {
  const JSON_URL = "{{ '/assets/json/projects/stationary-states/box_eigenbasis.json' | relative_url }}";
  let B = null, coeffs = [0.7, 0.0, 0.5, 0.0, 0.3, 0.0, 0.0, 0.0];
  let t = 0, playing = true, raf = null;

  function norm(c) {
    const s = Math.sqrt(c.reduce((a, v) => a + v * v, 0)) || 1;
    return c.map(v => v / s);
  }
  function density() {
    const c = norm(coeffs), x = B.x, n = x.length, re = new Float64Array(n), im = new Float64Array(n);
    for (let m = 0; m < c.length; m++) {
      if (!c[m]) continue;
      const ph = -B.E[m] * t, cr = Math.cos(ph), ci = Math.sin(ph), phi = B.phi[m];
      for (let i = 0; i < n; i++) { re[i] += c[m] * phi[i] * cr; im[i] += c[m] * phi[i] * ci; }
    }
    const d = new Float64Array(n);
    for (let i = 0; i < n; i++) d[i] = re[i] * re[i] + im[i] * im[i];
    return d;
  }
  function redraw() {
    const d = density();
    Plotly.react("ss-superpose", [{
      x: B.x, y: Array.from(d), type: "scatter", mode: "lines", fill: "tozeroy",
      line: { color: "#58a6ff", width: 1.5 }, fillcolor: "rgba(88,166,255,0.35)",
      hoverinfo: "skip"
    }], {
      paper_bgcolor: "rgba(0,0,0,0)", plot_bgcolor: "rgba(0,0,0,0)",
      margin: { l: 10, r: 10, t: 10, b: 20 },
      xaxis: { range: [0, B.L], showticklabels: false, showgrid: false, zeroline: false, fixedrange: true },
      yaxis: { range: [0, 9], showticklabels: false, showgrid: false, zeroline: false, fixedrange: true },
      showlegend: false
    }, { displayModeBar: false, responsive: true });
  }
  function tick() { if (playing) { t += 0.004; redraw(); } raf = requestAnimationFrame(tick); }

  function buildControls() {
    const box = document.getElementById("ss-controls");
    const presets = {
      "single mode": [0, 1, 0, 0, 0, 0, 0, 0],
      "two modes (beating)": [1, 0, 1, 0, 0, 0, 0, 0],
      "localised bump": [0.71, 0, 0.5, 0, 0.35, 0, 0.22, 0]
    };
    let html = '<div style="margin-bottom:.4rem;">';
    for (const k in presets) html += `<button class="btn btn-sm btn-outline-secondary" data-p="${k}" style="margin-right:.3rem;">${k}</button>`;
    html += `<button class="btn btn-sm btn-outline-secondary" id="ss-play">pause</button></div>`;
    for (let m = 0; m < 5; m++)
      html += `<label style="display:inline-block;width:110px;">c<sub>${m + 1}</sub> = <span id="ss-v${m}">${coeffs[m].toFixed(2)}</span></label>`
        + `<input type="range" min="0" max="1" step="0.01" value="${coeffs[m]}" data-m="${m}" style="width:150px;vertical-align:middle;margin-right:1rem;">` + (m % 2 ? "<br>" : "");
    box.innerHTML = html;
    box.querySelectorAll('input[type=range]').forEach(s => s.addEventListener("input", e => {
      const m = +e.target.dataset.m; coeffs[m] = +e.target.value;
      document.getElementById("ss-v" + m).textContent = coeffs[m].toFixed(2);
    }));
    box.querySelectorAll('button[data-p]').forEach(b => b.addEventListener("click", () => {
      coeffs = presets[b.dataset.p].slice(); t = 0;
      box.querySelectorAll('input[type=range]').forEach(s => {
        const m = +s.dataset.m; s.value = coeffs[m]; document.getElementById("ss-v" + m).textContent = coeffs[m].toFixed(2);
      });
    }));
    document.getElementById("ss-play").addEventListener("click", e => {
      playing = !playing; e.target.textContent = playing ? "pause" : "play";
    });
  }

  document.addEventListener("readystatechange", () => {
    if (document.readyState === "complete" && !window.__ssLoaded) {
      window.__ssLoaded = true;
      fetch(JSON_URL).then(r => r.json()).then(j => {
        B = j; buildControls(); redraw(); tick();
      });
    }
  });
})();
</script>

{% include video.liquid path="assets/video/projects/stationary-states/double_well_tunneling.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A state prepared entirely in the left well of a double well tunnels to the right well and back — the symmetric/antisymmetric splitting behind the covalent bond and the ammonia-inversion clock." %}

<div id="ss-tunnel" style="width:100%;height:420px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__ssTunnelLoaded) {
    window.__ssTunnelLoaded = true;
    fetch("{{ '/assets/json/projects/stationary-states/tunneling_splitting.json' | relative_url }}")
      .then(r => r.json())
      .then(fig => Plotly.newPlot("ss-tunnel", fig.data, fig.layout, { responsive: true }));
  }
});
</script>

<div class="caption">
    Tunnel-doublet splitting versus the half-separation of the two wells — a straight line on a log scale, i.e. exponential. Hover for the corresponding inversion period.
</div>

{% include video.liquid path="assets/video/projects/stationary-states/alpha_decay.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A quasi-bound state — trapped behind a Coulomb barrier rather than a genuine well — leaking through a thin vs. a thick barrier. A modest change in the barrier gives a very different lifetime: the same mechanism behind nuclear alpha decay." %}

<div id="ss-stark" style="width:100%;height:460px;"></div>
<script>
document.addEventListener("readystatechange", () => {
  if (document.readyState === "complete" && !window.__ssStarkLoaded) {
    window.__ssStarkLoaded = true;
    fetch("{{ '/assets/json/projects/stationary-states/stark_diagram.json' | relative_url }}")
      .then(r => r.json())
      .then(fig => Plotly.newPlot("ss-stark", fig.data, fig.layout, { responsive: true }));
  }
});
</script>

<div class="caption">
    Hydrogen's n = 1, 2, 3 levels fanning out as an electric field turns on. The n = 2 shell splits linearly (±3F) — the linear Stark effect, unique to hydrogen's degeneracy.
</div>

## Code

- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) —
  the split-operator propagator and the finite-difference eigensolver
  (`TDSE_Solver`), the analytic box / harmonic / hydrogen eigenbases and
  perturbation theory (`Perturbation_and_Basis_Methods`).
