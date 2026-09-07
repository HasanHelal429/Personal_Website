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

Every quantum-mechanics course opens the same way: find the states of
definite energy. Solve

$$ \hat{H}\,\psi_n(x) = E_n\,\psi_n(x), $$

get a ladder of energies $E_n$ and a set of wavefunctions $\psi_n$, and
everything else — how any state moves, how a spectrum responds to a
perturbation — follows from writing that state as a superposition of these.
This project builds that machinery from scratch: the analytic eigenbases for
the three exactly-solvable problems, a finite-difference eigensolver for
everything else, and time evolution by phase.

## Three problems with exact answers

The particle in a box, the harmonic oscillator, and the hydrogen atom are
the three potentials whose eigenvalue problem can be solved in closed form —
and between them they set every intuition that comes later.

{% include figure.liquid path="assets/img/projects/stationary-states/three_models.png" title="Three exactly-solvable potentials and their energy ladders" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    A rising ladder that spreads out (box, E<sub>n</sub> ∝ n²), an even ladder (oscillator, E<sub>n</sub> = ω(n+½)), and a ladder that bunches toward a limit (hydrogen, E<sub>n</sub> = −1/2n²). The wavefunctions gain one node per rung.
</div>

The box has a subtlety the other two don't: its levels come in **degenerate
groups** once you go to more than one dimension. $E_{n_x n_y} \propto n_x^2 +
n_y^2$, so $(2,1)$ and $(1,2)$ have exactly the same energy — the same
wavefunction rotated 90°, held together by the square's symmetry.

{% include figure.liquid path="assets/img/projects/stationary-states/box_2d_eigenstates.png" title="Eigenstates of a two-dimensional box" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The lowest six eigenstates of a 2D box (red/blue = sign of ψ). (2,1) and (1,2) are degenerate at E = 2.5, as are (3,1) and (1,3) at E = 5 — a degeneracy that comes entirely from the geometry.
</div>

Hydrogen carries the same idea further: its degeneracy is large enough
(every $\ell$ and $m$ at a given $n$) that the orbitals you draw are really a
choice of basis within a degenerate shell. The angular part is a real
spherical harmonic; the radial part carries the nodes.

{% include figure.liquid path="assets/img/projects/stationary-states/hydrogen_orbitals.png" title="Hydrogen orbitals" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    |ψ|² in the x–z plane for the 1s, 2p<sub>z</sub> and 3d<sub>z²</sub> orbitals (z vertical), built from the analytic radial functions and real spherical harmonics — the shapes chemistry inherits.
</div>

## Any state is a superposition

The eigenstates are a basis. Write any wavefunction as
$\psi(x) = \sum_n c_n\,\phi_n(x)$ once, and its whole future is trivial:
every coefficient just turns its own phase at its own rate.

{% include figure.liquid path="assets/img/projects/stationary-states/superposition_cycle.png" title="Evolving a state in an energy eigenbasis" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    Project onto the eigenbasis, multiply each coefficient by e<sup>−iE<sub>n</sub>t</sup>, sum back up. No stepping — the dynamics is exact for as long a time as you like. Keeping more modes shrinks the truncation error 1 − Σ|c<sub>n</sub>|² monotonically toward zero.
</div>

Drop a Gaussian into a box and evolve it and the interference of all those
phases produces something startling — the wavepacket shreds into a fine
ripple, then **reassembles**. At $t = \tfrac12 T_{\rm rev}$ it reforms at the
mirror-image position; at $t = T_{\rm rev}$ it is back exactly where it
started. This _quantum revival_ is a pure consequence of the box spectrum
being $E_n \propto n^2$: after $T_{\rm rev} = 4mL^2/\pi\hbar$ every phase has
come back to a multiple of $2\pi$ at once.

{% include video.liquid path="assets/video/projects/stationary-states/wavepacket_in_a_box.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A Gaussian wavepacket in a box, reconstructed from 60 eigenstates. It bounces, disperses into an interference carpet, and revives — first at the reflected position (½ T_rev), then fully (T_rev)." %}

The harmonic oscillator has the opposite personality. Its spectrum is
_evenly_ spaced, so a Gaussian whose width exactly matches the oscillator's
natural length stays a Gaussian forever — a **coherent state**, sliding back
and forth along the exact classical trajectory $\langle x\rangle(t) = x_0\cos
\omega t$ with no spreading at all. Here the numerics reproduce that
trajectory to $5\times10^{-9}$, with no free parameters.

{% include video.liquid path="assets/video/projects/stationary-states/coherent_state.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A coherent state of the harmonic oscillator: an equal-phase-spacing superposition that rides the classical trajectory (white dot) rigidly. The closest a quantum state comes to a classical particle." %}

### Build your own superposition

Set the weight of each box eigenstate and watch $|\psi(x,t)|^2$ evolve. A
single mode is a standing wave that only turns its phase — its density never
moves. Mix in a second and the two beat against each other; mix in several
and you get localisation, motion, and (given long enough) revival.

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

## When there is no formula: diagonalise the grid

Almost no potential has a closed-form spectrum. The general method is
blunt and reliable: sample $x$ on a grid, write the Laplacian as a
three-point finite difference, and $\hat{H}$ becomes a sparse matrix whose
lowest eigenpairs are exactly what a Lanczos solver is built for.

Checked against the three cases that _do_ have formulas, the finite-
difference eigenvalues match to the grid's second-order accuracy and improve
by the expected factor of four each time the spacing is halved — and the 2D
and 3D degeneracies come out with the right multiplicities. Past that, the
same code goes wherever the potential does.

## A double well, and the origin of the chemical bond

Two wells side by side. If they were infinitely far apart, the ground state
would be doubly degenerate — a particle in the left well, or in the right.
Bring them close enough that the wavefunction can leak through the barrier
between them and that degeneracy splits into a **symmetric / antisymmetric
pair** separated by a tiny gap $\Delta E$. The splitting here tracks the
semiclassical (WKB) tunnelling estimate to about 1%.

That gap is not just a number. A state localised in one well is the equal
superposition $(\psi_S - \psi_A)/\sqrt2$ of the two — and because the pair
have slightly different energies, that superposition oscillates: the particle
tunnels wholesale to the other well and back, with period $2\pi/\Delta E$.

{% include video.liquid path="assets/video/projects/stationary-states/double_well_tunneling.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A state prepared entirely in the left well tunnels to the right well and back. The splitting between the symmetric and antisymmetric eigenstates sets the period. This is the covalent bond (the shared, symmetric state sits lower) and the ammonia-inversion clock in one picture." %}

$\Delta E$ depends _exponentially_ on how far the particle has to tunnel —
widen the wells and the splitting collapses by orders of magnitude over a
Bohr radius or two:

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
    Tunnel-doublet splitting versus the half-separation of the two wells, on a log scale — a straight line, i.e. an exponential. The hover shows the corresponding inversion period.
</div>

## Quasi-bound states: alpha decay

Not every "bound" state is really bound. Put a well behind a Coulomb barrier
— the shape a proton sees inside a nucleus, or an alpha particle trying to
leave one — and a state with _positive_ energy can still be trapped, held in
by the barrier rather than by a genuine well. It is only **quasi**-bound: it
leaks.

{% include video.liquid path="assets/video/projects/stationary-states/alpha_decay.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A quasi-bound state (dotted line = its energy) tunnelling out through the Coulomb barrier. The density is shown on a square-root scale so the faint escaping wave is visible; the trapped fraction decays exponentially in time." %}

The decay is a clean exponential, and its rate is set by the same
tunnelling-through-a-barrier factor as the double well — only now the barrier
is the Coulomb repulsion, and the "attempt frequency" is how often the
trapped particle hits the wall. Because that factor is exponential in the
barrier, a small change in the particle's energy changes the lifetime by
enormous factors: the reason nuclear alpha-decay half-lives span from
microseconds to longer than the age of the universe (the Geiger–Nuttall
law).

## Perturbation theory: shifting a spectrum without re-solving

Once you have the eigenstates, you rarely want to re-diagonalise for a small
change. Perturbation theory gives the new energies directly — a first-order
shift $\langle n|\hat{H}'|n\rangle$ plus a second-order sum over all the other
states, each weighted by $|\langle m|\hat{H}'|n\rangle|^2$ over the energy gap
$E_n - E_m$. Tested
against exact diagonalisation of the truncated $H_0 + H'$, the first-order
error scales as the perturbation strength squared and the second-order error
as its cube — and, tellingly, the series visibly _diverges_ once the
perturbation is no longer small.

The classic case is hydrogen in an electric field. The $n=2$ shell is
four-fold degenerate, so first order means diagonalising $\hat{H}' = Fz$
_within_ that shell. Building the $4\times4$ matrix from the actual
wavefunctions and diagonalising gives two states that shift by exactly
$\pm 3F$ (atomic units) and two that do not move at all — the shifted states
being the combinations $(2s \pm 2p_z)/\sqrt2$. This is a _linear_ Stark
effect, unique to hydrogen's degeneracy; every other atom shifts only
quadratically.

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
    The hydrogen n = 1, 2, 3 levels fanning out as the field is turned on. Dotted lines are levels that stay put; solid lines split linearly. The n = 2 slope of ±3F is computed here from the ⟨2s|z|2p_z⟩ matrix element, not assumed.
</div>

## Code

- [`Physics-Simulations`](https://github.com/HasanHelal429/Physics-Simulations) —
  the split-operator propagator and the finite-difference eigensolver
  (`TDSE_Solver`), the analytic box / harmonic / hydrogen eigenbases and
  perturbation theory (`Perturbation_and_Basis_Methods`).
