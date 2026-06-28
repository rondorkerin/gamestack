# Engine overlay: Three.js / web

Loaded when the target engine is Three.js (web/WebGL). **No curated engine pack exists
yet** — this is a gamestack roadmap gap. This overlay gives the agent enough to implement
from the design specs plus general Three.js knowledge, the same way the design brain works
engine-agnostically.

## Engine pack
None slotted in yet. (Roadmap: curate or build a `threejs` pack.) Implement directly from
the bible using general Three.js / web knowledge.

## Spec → implementation mapping (no pack; first-principles)

| Bible / design spec | Three.js / web approach |
|---------------------|-------------------------|
| World structure, navigation (`world.md`) | `THREE.Scene` graph; instanced meshes for repeated procgen objects |
| Systems / rules (`systems.md`) | Plain TS modules; an ECS lib (e.g. miniplex/bitECS) for many entities |
| Procgen output (`constraints.md`) | Generate data → build geometry at load; keep data the source of truth |
| Combat / game feel (`combat-design`) | Fixed-timestep loop; decouple render from sim; juice via post-processing |
| UI / readability | DOM/CSS overlay or a canvas UI layer; keep HUD out of the WebGL context |
| Performance | Instancing, frustum culling, draw-call budget, LOD; profile with stats.js |
| Ship | Bundler (Vite); code-split heavy assets; compress textures (KTX2/Basis) |

## Conventions & gotchas
- Web has a hard draw-call and memory budget — lean even harder into instancing and the
  "hand-author the spine, proceduralize the tissue" ethos; do not spawn thousands of
  individual meshes.
- Keep the sim deterministic and data-driven so `constraints.md` remains authoritative
  and content stays reproducible.

## Handoff note
`engine-router` passes the bible as the engine-independent contract. State the no-pack gap
to the user and implement from the specs.
