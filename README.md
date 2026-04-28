# Simplex Screensaver

A fullscreen browser screensaver that visualizes the simplex algorithm walking across randomly generated 3D convex polytopes.

The goal is to make simplex feel embodied and spatial without faking the mathematics: the shape is defined by real linear constraints, the vertices are computed from those constraints, and the animated path follows actual improving pivots until it reaches an optimum.

## Demo

Open `index.html` in a modern browser.

```bash
open index.html
```

No build step is required. The page uses Three.js from jsDelivr via an import map, so it does need network access when loaded for the first time.

Press `f` to toggle fullscreen.

## What It Shows

Each scene is a bounded 3D linear program:

- Constraint halfspaces define a feasible polytope.
- Vertices are enumerated from triples of tight constraints.
- Edges are inferred from shared active constraints.
- Objective values color the feasible vertices.
- The simplex path moves along improving adjacent vertices.
- All adjacent pivot options are previewed before the chosen move.
- The chosen pivot uses the largest adjacent objective-value increase, a geometric analogue of Dantzig's rule.
- At the optimum, neighboring non-improving edges are shown as blocked.
- Between scenes, the constraints morph, causing the feasible region itself to reshape.

The animation is intentionally screensaver-like: ambient, slow, and visual-first, but still grounded in the structure of the algorithm.

## Epistemic Accuracy

The main accuracy choices are:

- The polytope is generated from an H-representation: inequalities of the form `a · x <= b`.
- Vertices are produced by solving intersections of constraint triples, then checking feasibility against all constraints.
- The adjacency graph comes from shared tight constraints.
- Simplex steps only move to adjacent vertices with strictly better objective value.
- Among improving adjacent vertices, the pivot chooses the largest objective-value increase.
- The optimum is only marked when no adjacent improving move remains.
- Scene transitions interpolate constraints rather than directly tweening arbitrary mesh vertices.

That last point matters: morphing constraints means the topology can change during transitions as faces appear, disappear, split, or merge. Those moments can be visually messier than a pure mesh tween, but they preserve the idea that the feasible region is made by inequalities.

## Visual Language

- Blue/cyan vertices indicate lower-to-mid objective values.
- Amber vertices indicate considered improving pivots, with stronger edges showing larger improvement.
- White indicates the current or chosen simplex move.
- Gold-white marks the optimum.
- Muted blue indicates adjacent moves that do not improve the objective.
- The translucent polytope body is unlit to avoid glare and keep the path readable.

## Implementation

Everything lives in a single `index.html` file.

Main modules:

- `LP`: generates bounded 3D linear programs.
- `VertexEnum`: enumerates feasible vertices and builds adjacency.
- `Simplex`: chooses and returns the simplex traversal path.
- `SceneRenderer`: owns Three.js scene, camera, lights, renderer, and bloom.
- `Polytope`: builds meshes, vertex markers, trails, candidate hints, and optimum halos.
- `Animator`: runs the state machine and coordinates traversal plus transitions.

Dependencies are loaded in the browser:

- Three.js
- `ConvexGeometry`
- `EffectComposer`
- `RenderPass`
- `UnrealBloomPass`
- `OutputPass`

## Design Notes

The project is inspired by the ambience of classic After Dark-style screensavers: something that can run quietly, be watched passively, and still reveal structure over time.

A few tradeoffs are deliberate:

- Random LPs are filtered for visual legibility, but still must be valid LPs with real simplex paths.
- Very short edges are avoided because they are technically correct but visually muddy.
- Transition frames are sampled from interpolated constraints for smoother morphing while avoiding a full geometry rebuild on every animation frame.
- The camera is smoothed and centroid-biased so the animation feels contemplative rather than like a debugging tool.

## Publishing

This is a static site. You can publish the repository root with GitHub Pages, Netlify, Vercel, or any static host.

If publishing with GitHub Pages, set the Pages source to the repository root.

## License

MIT.