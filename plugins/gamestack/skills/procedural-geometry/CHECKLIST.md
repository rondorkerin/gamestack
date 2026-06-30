# Procedural Geometry — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for generating geometry that is correct and varied. Run against a generation pipeline, a terrain/structure/scatter pass, or a batch of generated meshes. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Two orders matter:** (1) representation → extractor → erosion → LOD are *architecture* decisions, fixed up front from the design's topology needs. (2) Correctness (topology validator) is a non-negotiable floor; variety (oatmeal test) is the gate above it. Don't ship geometry that fails the floor; don't call generated geometry "done" until it passes the gate.

---

## A · Noise as the substrate

**Do**
- [ ] Pick the noise family by its **artifact** (value = cheap/smooth; Perlin = default; Simplex when banding shows or you need 4D), seeded **deterministically** and the seed **recorded**.
- [ ] Build terrain from fBm (lacunarity ≈ 2, gain ≈ 0.5 to start) then **modulate** — multifractal for heterogeneous roughness, ridged for sharp ridgelines.
- [ ] **Domain-warp** (`f(p + fbm(p))`) for organic flow; carve caves from a **3D** noise isosurface.

**Don't**
- [ ] Don't ship plain fBm as terrain (blobby, no ridgelines/drainage — the "fake" look).
- [ ] Don't free-roll a continuous parameter space (most of it is oatmeal) — ship hand-authored range *presets* per biome.
- [ ] Don't assume Simplex is "better" — its 2D/3D speed edge is small.

**Test for** — Rendered *lit* (not gray), is there axis-aligned banding? Does the field show continuous ridges + valleys, not isolated bumps? Are carved caves **connected** (flood-fill) and surface-reachable? Same seed → same field?

---

## B · Terrain generation (representation, extraction, erosion, LOD)

**Do**
- [ ] Choose **heightfield** unless the design needs overhangs/caves/arches; choose **voxel/SDF** only for true 3D topology — and commit it up front.
- [ ] Pick the extractor: **marching cubes** for smooth/simple (+ Transvoxel seams, weld duplicates), **dual contouring** for sharp features + easy LOD (+ non-manifold repair).
- [ ] Run **hydraulic + thermal erosion** as an **offline bake**, then **cache** it.
- [ ] LOD + chunk the terrain; fill LOD-boundary **cracks** with skirts or edge-matching.

**Don't**
- [ ] Don't ship voxels for flat terrain (huge cost, unused) or try to fake overhangs on a heightfield (impossible).
- [ ] Don't use marching cubes where sharp architecture matters (it rounds every corner).
- [ ] Don't run erosion or meshing per frame / on load (stalls) — bake and cache.
- [ ] Don't let chunk edges open see-through cracks or pop unmasked.

**Test for** — Does any requirement need two surfaces over one column? Extract a cube corner: rounded (MC) or sharp (DC) as intended? Post-erosion: connected drainage, talus-respecting slopes, sediment-smoothed valleys? At a LOD seam (grazing angle): no through-gap, no unmasked geometry jump?

---

## C · Structural & architectural generation

**Do**
- [ ] Match method to form: **L-systems** for organic growth, **shape grammars** for architecture, **WFC/model synthesis** for constrained tile assembly.
- [ ] Put the craft in the **rules/tileset/constraints** (architectural alignment, adjacency, botanical constraints) — the algorithm only enforces.
- [ ] **Hybridize:** hand-place silhouette landmarks + set pieces; generate the fill between them under constraints.
- [ ] Add a **navigability/connectivity validator** (WFC guarantees neither; add backtracking/restart for contradictions).

**Don't**
- [ ] Don't use L-systems for rectilinear architecture or shape grammars for organic plants (wrong tool).
- [ ] Don't ship a small generic tileset/grid and expect variety (tileset-shuffle sameness).
- [ ] Don't fix oatmeal structures with **more randomness** — fix with richer constraints + hand anchors.

**Test for** — Does the same ruleset (varied seeds) read as the *same species/style* with individual variation? Do buildings obey real logic (floors align, windows don't clip corners, human-scale doors)? Does every tile junction satisfy adjacency and stay navigable? Across many seeds — perceptually varied, or the same pieces rearranged?

---

## D · Mesh topology correctness (the non-negotiable floor)

**Do**
- [ ] Emit every triangle with **consistent winding** (CCW front faces) so normals point outward; derive normals from winding deliberately.
- [ ] Keep meshes **manifold** (≤2 faces/edge, no fan-vertices); **watertight** (no boundary edges) where collision/physics/booleans need it.
- [ ] Reject/repair **degenerate triangles** (zero area) and **degenerate UVs** (collapsed to a line/point); weld coincident vertices.

**Don't**
- [ ] Don't trust the generation order to produce correct normals.
- [ ] Don't pass non-manifold/degenerate meshes downstream (silent until physics/UV/lightmap chokes).
- [ ] Don't regenerate static geometry per frame or on the main thread (stutter); don't let worker queues outrun consumption (OOM).

**Test for** (the **mesh topology validator**, block commit on failure) — Single-sided render from all sides: any black/invisible faces (reversed winding)? Edge-manifold (≤2 faces/edge)? Any zero-area triangles or NaN normals? Any collapsed UV triangles? Watertight where required (zero open edges)? Profile under heavy generation: main-thread generation ≈ 0, no upload-burst frame spikes, no stutter moving through fresh terrain?

---

## E · Vegetation & scatter

**Do**
- [ ] Place scatter with **blue noise (Poisson-disc)**; precompute tiling sets for seamless cross-chunk scatter.
- [ ] **Mask** by slope / altitude / moisture / biome (Whittaker temp×moisture) + a density field.
- [ ] Render via **GPU instancing** with GPU culling + LOD/impostors; emit **instance references + transforms**, never bespoke per-plant meshes.

**Don't**
- [ ] Don't grid-place (obvious tell) or pure-random-place (clumps + bald voids).
- [ ] Don't scatter context-blind (trees on cliffs/underwater, jungle plants in snow, uniform density).
- [ ] Don't draw one object per plant or render off-screen/distant instances at full detail.

**Test for** — From above: no rows/grid, no bald-patch-next-to-clump, minimum spacing held? No tree on a vertical slope or above the tree line; species matches biome; density tracks the mask? Draw calls ≈ (meshes × materials) not (instances); off-screen culled; CPU near-flat as instance count rises?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — fix before generating, validate every batch, **block commit** on failure:

- [ ] **Representation + extractor** (heightfield vs voxel/SDF; MC vs DC) → architecture decision, fixed up front from topology needs.
- [ ] **Mesh topology validator** (winding/normals · manifold · no degenerate tris/UVs · watertight where required · weld threshold) → the correctness floor.
- [ ] **Erosion bake + cache** and **chunk crack-free contract** → erosion never per-frame; assert no through-gap between adjacent-LOD chunks.
- [ ] **Connectivity/navigability validators** (caves reach surface; WFC/dungeons navigable) → flood-fill / pathfind check.
- [ ] **Noise/erosion/biome presets** (hand-authored parameter *ranges*, seeds recorded) → not free-rolling sliders.
- [ ] **Scatter = instance references + transforms** (small vetted library) → reject bespoke per-plant geometry and grid-aligned placement.
- [ ] **Threading + caching** (generate on workers, cache chunks, budget GPU uploads) → architectural, not an optimization.

> Topology validators catch **correctness**, not beauty. Gate every generated batch through `procgen-review`'s **oatmeal test** for *geometric* sameness (the variety gate above this floor); make it *legible* with `level-design`; give it *meaning* with `procedural-generation` / `worldbuilding-and-lore`.
