---
layout: page
title: Tidal Disruption of a Star
description: A self-gravitating SPH star, built from a Lane-Emden polytrope and validated to equilibrium, torn apart on a close encounter with a black hole — plus the slower, stranger runaway that happens when it orbits instead of plunging.
img: assets/img/projects/tidal-disruption/tidal_disruption_beta3_thumb.png
importance: 3
category: General Relativity
related_publications: false
chart:
  plotly: true
---

A star wandering too close to a black hole doesn't fall in cleanly — the
hole's gravity pulls harder on the near side than the far side, and the
difference is enough to shred it. This is a from-scratch simulation of
that process: a real, self-gravitating star, built particle by particle
from stellar-structure theory, thrown at a black hole and watched as it
comes apart.

## Building a star out of particles

The star is smoothed-particle hydrodynamics (SPH): a few thousand
particles, each carrying mass, position, and velocity, that feel each
other's gravity plus a pressure force built from the local density. The
initial particle positions aren't arbitrary — they're Monte-Carlo sampled
from a **Lane-Emden polytrope** (index n=1.5, the standard model for a
low-mass convective star), so the star starts close to its own
hydrostatic equilibrium: pressure gradient balancing self-gravity at
every radius, by construction rather than by luck.

"Close to" still needs to be shown, not assumed. Relaxed under mild
damping and then re-run for ten dynamical times with **no** damping at
all, the settled star holds its shape — the radial density profile is
time-independent, matches the analytic Lane-Emden target to under 1%
through the bulk of the star, and energy is conserved to 0.09%. A single
fixed smoothing length under-resolves the sparse outer envelope relative
to the dense core, so each particle instead solves for its own adaptive
$h$ every step (Springel & Hernquist's ansatz); getting the density
self-term right in that iteration — and not, as an earlier version did,
accidentally excluding it — turned out to be the difference between a
10-30% systematic density bias and matching theory to 1%.

## The encounter

The black hole is a fixed point mass (its own recoil is negligible at
this mass ratio); the star is boosted onto a parabolic orbit — the
standard assumption for a star scattered in from a large, weakly-bound
orbit — parameterized by $\beta = r_t/r_p$, the ratio of the tidal
radius to how close the orbit actually brings the star to the hole.

{% include figure.liquid path="assets/img/projects/tidal-disruption/encounter_schematic.png" title="Tidal-encounter geometry" class="img-fluid rounded z-depth-1" %}

<div class="caption">
    The parabolic infall (standard for a star scattered in from a large, weakly-bound orbit), the tidal radius r_t, and the pericenter distance r_p it actually reaches — beta is just their ratio. Bigger beta means a deeper, more destructive plunge.
</div>

At $\beta=3$, a deep plunge, the star stretches into the classic curved
tidal debris stream within a single frame of pericenter passage, right
on the predicted timing.

{% include video.liquid path="assets/video/projects/tidal-disruption/tidal_disruption_beta3.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star disrupted into a tidal debris stream on a close, deeply-plunging encounter (beta=3). Colored by density; the black hole is the marker at the point of closest approach." %}

Run the same encounter 50 dynamical times longer and a second act
appears: about **54% of the star's mass stays bound** to the black hole,
and that debris measurably falls back — swinging past the hole again,
closer than the original pericenter, right around the time Kepler's
third law predicts for its most tightly-bound particles. The
fallback rate, binned by each particle's predicted return time, follows
a clean power law over four decades in time with a fitted slope of
**-1.31** — shallower than the idealized "frozen-in" $t^{-5/3}$
asymptote, which is itself expected: that exponent is a late-time limit,
and a centrally-concentrated n=1.5 star is well known to decline slower
than that near peak fallback, steepening only much later.

{% include video.liquid path="assets/video/projects/tidal-disruption/fallback_return.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="The same encounter, run 50 dynamical times longer and cropped in tight: the unbound majority of the star has receded far out of frame, but the bound ~54% falls back, swinging past the black hole again — closer than the original pericenter." %}

Swapping the black hole's force law for a Paczynski-Wiita
pseudo-potential — a cheap stand-in for genuine strong-field gravity,
exact at the ISCO and divergent at the Schwarzschild radius — on the
identical orbit isolates exactly what stronger gravity changes: peak
kinetic energy at pericenter rises 26%, and the leading edge of the
star's debris swings measurably closer in before escaping back out,
with energy conservation still under 0.15% throughout.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/tidal-disruption/newtonian_encounter.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/projects/tidal-disruption/pw_encounter.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true %}
    </div>
</div>
<div class="caption">
    The identical star on the identical orbit, only the black hole's force law changed: Newtonian point mass (left) vs. Paczynski-Wiita (right). The Paczynski-Wiita star swings measurably closer in before it escapes back out.
</div>

## A different kind of orbit: repeated partial feeding

A single deep plunge and a full circular orbit are two extremes; a
bound, eccentric orbit sits in between. Placed on an orbit around a
much lighter black hole ($M_\text{bh}=10\,M_\star$, $\beta=0.5$,
eccentricity $0.6$ — chosen after a beta scan as the narrow window
between "no measurable stripping" and "tears off over 10% of the star
in one pass"), the star survives each periastron passage largely
intact, shedding only a thin stream each time rather than disrupting
outright — the 90th-percentile spread of its remaining mass barely
moves, with roughly 0.2% of its particles pulled into an extended tail
per passage.

{% include video.liquid path="assets/video/projects/tidal-disruption/binary_feeding.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star on a bound, eccentric orbit (beta=0.5, e=0.6) around a black hole only 10x its own mass, shown over ~4.3 orbits. Camera follows the star's own center of mass. Each periastron passage strips a little more, repeated feeding rather than one-shot disruption." %}

## Steady feeding: a runaway on a circular orbit

A single deep plunge is the dramatic case, but it isn't the only way a
star meets a black hole. Placed on a circular orbit just inside its own
disruption threshold ($\beta=0.47$, barely past the point where nothing
strips at all), the star instead sheds a slow trickle of mass each
orbit — a much gentler process, at first indistinguishable from nothing
happening at all. But every trickle removes the star's own
loosely-bound outer envelope, which shrinks its effective Roche lobe,
which makes the next pass strip a little more: after enough orbits the
slow trickle runs away into full disruption.

{% include video.liquid path="assets/video/projects/tidal-disruption/circular_feeding_3d.webm" class="img-fluid rounded z-depth-1" autoplay=true loop=true muted=true controls=true caption="A star on a circular orbit (beta=0.47) sheds a slow trickle for several orbits before the stripping runs away into a full tidal breakup, spun into a debris stream trailing the black hole. Colored by density; camera recentered on the star's own (density-weighted) center of mass." %}

All 4000 particles above are real 3D positions, not a 2D projection —
here's the same run as an actual interactive point cloud. Drag to
rotate, scroll to zoom, or press Play to watch the full ~12-orbit
breakup unfold from any angle:

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

An interactive version of the black hole itself follows the same logic
one step further: it actually accretes (its mass grows live as
particles cross an accretion radius), and pressing a key spawns another
star on the fly, on its own Kepler orbit around whatever the hole has
grown into so far — a genuine, if brute-force, N-body system once more
than one star is in play.

## Code

- OpenGL Physics (`06_tidal_disruption`) — the SPH star, its Lane-Emden
  initial conditions, and the black-hole encounter machinery — not yet
  public
