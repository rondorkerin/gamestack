# Procedural Geometry — Guide

The discipline of generating geometry that is **correct** (watertight, manifold, properly wound, performant) and **varied** (perceptually distinct, not parametric oatmeal): terrain, meshes, structures, scatter. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Ken Perlin's noise papers (1985 "An Image Synthesizer"; 2002 "Improving Noise"), Musgrave's procedural-fractal-terrain chapter in Ebert et al. *Texturing & Modeling*, Inigo Quilez on domain warping/fBm, Lorensen & Cline's marching cubes (1987) and Ju et al.'s dual contouring (2002), Sebastian Lague's erosion series, Lindenmayer's L-systems, Parish & Müller's shape grammars, Paul Merrell's model synthesis (≈ Gumin's wave function collapse), Oskar Stålberg's Townscaper/Bad North, Bridson's Poisson-disc sampling, and No Man's Sky's GDC world-generation talk. Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round3-technical/T4`).

> **The central law:** *a single noise field is oatmeal, and "designed-looking" is a constraint problem.* Structure comes from modulating noise (multifractal, domain warping) and from erosion — not from more octaves; designed-looking structures come from hand-authored rules/tilesets and hand-placed anchors — not from the algorithm. Correctness is non-negotiable and machine-checkable; variety is gated by the oatmeal test.

> For the *content/narrative* version of the oatmeal problem see `procedural-generation`; for making generated geometry *legible* see `level-design`; for *gating* it see `procgen-review`.

---

# Sub-domain A — Noise as the Geometry Substrate

> Noise is the raw material of procedural geometry, but raw noise is the "fake" look. Pick the family by its *artifact*, build terrain from fBm, then **break fBm's uniformity** — that is where believability lives. ✅ (Perlin 1985/2002; Musgrave; Quilez.)

## A.1 Pick the noise family by its artifact, not its reputation

**Value** noise interpolates a per-lattice random value (cheapest, smooth, low-stakes). **Perlin/gradient** noise interpolates a dot product of gradients (general default). **Simplex** (Perlin 2001) uses a simplex lattice to cut directional banding and scale cheaply to 4D+. ✅ (Perlin won a 1997 Academy Award for the original; "Improving Noise" fixed lattice-boundary discontinuities.) The Simplex speed edge is large in 4D+, "slighter for low dimensions" — so choose by where banding shows, not by reputation.

- **Test for:** render the noise as a *lit/normal-mapped* surface, not a gray heightmap — look for axis-aligned bands or a visible grid; if present and load-bearing, switch to Simplex or warp the domain. Confirm deterministic seeding.
- **Failure mode — Directional banding / grid tell:** Perlin's grid gradients band along axes — invisible in flat gray, obvious once the field drives lighting.

## A.2 Build from fBm, then modulate it (multifractal / ridged)

Stack octaves as fBm (lacunarity ≈ 2.0, gain ≈ 0.5 to start), then *modulate*: multifractal scales each octave's amplitude by the running value (heterogeneous roughness); ridged multifractal (`1 - |noise|` per octave) carves sharp ridgelines. ✅ (Musgrave: real terrain has *non-constant* fractal dimension — smooth valleys, rough ridges — that plain fBm can't express, yielding "isolated peaks" with no ridge structure.)

- **Test for:** plain fBm at equal octaves has rounded blobby peaks and no connected ridgelines; a believable range shows continuous ridges and valleys. More octaves add detail, not structure.
- **Failure mode — Blobby "noise terrain":** equal roughness everywhere, no drainage, no ridgelines — reads as a heightmap demo, not a place.

## A.3 Domain-warp for organic flow; 3D noise for caves

Domain warping evaluates `f(p + fbm(p))` (nested 1–2 levels) to introduce flow/swirl — rivers, marble, wind-carved rock — that octave-stacking can't. ✅ (Quilez: "distort the domain with another function g(p) before we evaluate f"; intermediate warp fields double as free masks.) Worley/cellular noise gives cell patterns; carve caves by thresholding a **3D** noise isosurface (≈ 0 ± ε; low freq → caverns, high → veins). ⚠️ (cave-by-isosurface is a well-known community technique.)

- **Test for:** a warped field shows directional channels/swirls absent from the un-warped field; carved caves must be *connected* (flood-fill) and reach the surface.
- **Failure mode — Cloudy sameness / disconnected caves:** un-warped fBm reads as static cloud; naive 3D-noise caves leave sealed, unreachable pockets.

**→ Procedural / headless implication.** Noise type, seed, frequency, dimension, lacunarity/gain, fBm-vs-multifractal are all safe generator parameters — **but record the seed** (a flagged region must be reproducible) and ship hand-authored *parameter-range presets* (mountains/plains/badlands), not a free-rolling continuous slider — most of that space is oatmeal. Cave connectivity/reachability is not guaranteed by noise; validate it.

---

# Sub-domain B — Terrain Generation

> Heightfields are cheap and 2.5D; voxels/SDF are expensive and truly 3D. Pick the representation from the design's *topology* needs, extract the mesh with the right algorithm for your sharp-feature/LOD needs, and **run erosion** — that is the step that turns "noise" into "terrain". ✅

## B.1 Default to heightfields; reach for voxels/SDF only for true 3D

A heightmap stores one height per (x,z) column — cheap, simple to LOD/collide, but **physically incapable** of overhangs, arches, or caves (those need two surfaces over one column). Voxel/SDF stores a 3D scalar field and meshes it — true 3D topology at large memory/meshing cost. ✅ (No Man's Sky uses voxels *because* its worlds need caves/arches; most terrain ships as heightfields because 2.5D is enough.)

- **Test for:** does any requirement actually need two surfaces over one column? No → heightfield (voxels are over-engineering). Yes → no heightfield parameter tuning can fake it.
- **Failure mode — Wrong representation:** voxels for flat terrain (huge cost, unused capability) or faking overhangs on a heightfield (impossible; vertical-wall artifacts).

## B.2 Choose the extractor by sharp-feature need and LOD strategy

**Marching cubes** (Lorensen & Cline 1987 — most-cited SIGGRAPH paper) puts vertices on cube *edges*: simplest/fastest/best-documented, but rounds sharp features, emits duplicate vertices (blocks smooth shading), and needs Transvoxel to stitch chunk LODs. **Dual contouring** (Ju, Losasso, Schaefer & Warren 2002) puts one vertex *inside* each cell from Hermite data (value + gradient): preserves sharp corners and natively joins different-sized octree leaves (easy LOD), but can emit non-manifold output. ✅

- **Test for:** extract a cube corner — MC rounds it, DC preserves it. Inspect MC for duplicate vertices, DC for non-manifold edges (each algorithm's characteristic defect).
- **Failure mode — Rounded features (MC) / non-manifold cells (DC):** MC for architecture loses every corner; DC without manifold-checking leaks bad geometry into physics/UV.

## B.3 Run erosion to add the structure noise cannot

After the base heightfield, run **hydraulic erosion** (droplet/sediment simulation → drainage networks, V-valleys) and **thermal erosion** (talus-angle slumping → realistic scree). ✅ (Lague's droplet sim "traces each droplet downhill, picking up and depositing sediment"; this adds the connected drainage and ridge-to-valley grading fBm and even multifractal lack.) It is an expensive **offline bake**, never per-frame.

- **Test for:** post-erosion, flow is *connected* (ridges → valleys → drainage), slopes respect the talus angle, valleys show sediment smoothing; compare pre/post — erosion visibly carves channels.
- **Failure mode — Un-eroded noise / over-eroded mush:** none = video-game-noise look; too much = everything flattened to dramaless hills.

## B.4 LOD and chunk the terrain, and fill the cracks

Stream terrain as distance-based LOD chunks (quadtree chunked LOD or geometry clipmaps for heightfields; octree for voxels). At LOD boundaries prevent **cracks** with skirts (vertical aprons hiding the gap) or edge-matching (Transvoxel for MC, native for DC). ✅ (Chunked LOD uses "a quadtree of chunks with skirts to fill cracks rather than force each edge to match"; clipmaps stream constant-cost patches with cheap CPU traversal.)

- **Test for:** sight a LOD seam at grazing angle — no through-gap to the skybox (crack), no unmasked geometry jump (pop). LOD selection stays cheap (hundreds of node tests), not per-vertex.
- **Failure mode — Cracks and LOD pop:** mismatched edge resolutions open hairline see-through gaps; abrupt swaps pop. Skirts hide cracks; geomorph/dither hides pop.

**→ Procedural / headless implication.** Representation and extractor are *architecture* decisions — fixed once, up front, from topology needs (a generator can't switch them mid-world). Erosion is a **bake** the generator runs once per region and **caches** (re-running on load stalls). Chunk size, LOD distances, skirt depth are tunable, but the **crack-free contract** is validator-checkable: generate adjacent chunks at different LODs, assert no through-gap.

---

# Sub-domain C — Structural & Architectural Generation

> The "designed" quality is produced by **constraints and hand-authored anchors**, not by the generator. Pick the method by the form: L-systems for organic growth, shape grammars for architecture, WFC/model synthesis for constrained tile assembly. This is the geometry engine *underneath* `level-design`. ✅

## C.1 L-systems for self-similar organic growth

String-rewriting production rules interpreted as turtle commands (forward, turn, push/pop) generate branching, self-similar forms — trees, plants, vines, coral, rivers. ✅ (Lindenmayer 1968; standard in vegetation.) Wrong tool for rectilinear architecture.

- **Test for:** the same ruleset (with stochastic rules/seeds) yields *recognizably the same species* with individual variation, not random scribbles.
- **Failure mode — Fractal-obvious or chaotic:** too-regular = obvious fractal; unconstrained stochastic = non-plants. Bias toward botanical constraints (gravity, taper, phototropism).

## C.2 Shape grammars for architecture — the grammar is the building's identity

Start from a mass, split into floors/facades, subdivide facades into tiles (windows, doors) via parameterized production rules; keep architectural constraints (alignment, symmetry, plausibility) in the grammar. ✅ (Parish & Müller "Procedural Modeling of Cities" 2001 → CGA / CityEngine; Wonka et al. "Instant Architecture" 2003. Noted limit: rule-based output "lacks diversity without careful authoring".)

- **Test for:** floors align, windows are regularly spaced and don't clip corners, roofs cap cleanly, door height ≈ human. No window bisected by a floor slab.
- **Failure mode — Impossible or repetitive buildings:** windows through corners, floating floors, doors to nowhere — or every building identical from too-thin variation.

## C.3 WFC / model synthesis for constraint-consistent tile assembly

A constraint solver places tiles consistent with **authored adjacency rules** — dungeons, buildings, levels, towns. ✅ (Gumin's WFC 2016 ≈ Merrell's model synthesis 2007; shipped in Bad North, Townscaper, Caves of Qud. Stålberg computes adjacency on import and, for Bad North, "selects tiles so the zone stays navigable at each step".) Naive WFC can hit unsatisfiable contradictions — add backtracking/restart.

- **Test for:** every junction satisfies adjacency (no illegal neighbors), the result is *connected/navigable* where required, contradictions are handled (not left as holes). Run many seeds — varied, or the same tileset shuffled?
- **Failure mode — Contradiction holes & tileset-shuffle sameness:** naive WFC leaves holes; a small generic tileset makes every output look like the same pieces rearranged (geometric oatmeal).

## C.4 "Looks designed" = constraints + hand-placed anchors (hybridize)

Hand-place the landmarks, set pieces, and named structures (silhouette beats, boss arena, key town); let the generator fill *between* them under constraints. ✅ (Townscaper runs WFC + marching cubes on an *irregular* half-edge grid so output curves organically, not as a voxel grid; Bad North's "large hand-modeled tiles give smoothly curving beaches and chunky houses." Same algorithm + generic grid/tileset = generic output.)

- **Test for:** run `procgen-review`'s oatmeal test on a batch — perceptually distinct, or mathematically-different-but-visually-same? If the latter, fix with richer constraints/tilesets and hand-placed anchors, **not** more randomness.
- **Failure mode — Grid tell / oatmeal structures:** regular-grid output reads as generated; uniform parametric variation of one template = 10,000 perceptually-identical buildings.

**→ Procedural / headless implication.** The rules/tilesets/constraints **are** the craft surface — author them; the algorithm only enforces. Add a **navigability/connectivity validator** (WFC guarantees neither). The hybrid (hand-anchor + constrained fill) is the only reliable path to designed-looking geometry at scale — the geometric statement of `procedural-generation`'s hybrid model, gated by the oatmeal test.

---

# Sub-domain D — Mesh Generation & Topology Correctness

> Most procedural-mesh bugs are **silent topology bugs** — they look fine in one lighting/preview condition and break downstream. A generator has no eyes; correctness must be machine-enforced. This whole sub-domain is the **mesh topology validator**. ✅

## D.1 Wind triangles consistently so normals point outward

Emit every triangle with one consistent winding (typically CCW front faces) so the right-hand-rule normal points *outward*; assign normals from winding deliberately, not from generation order. ✅ (Procedural meshes "commonly have normal problems even when the shape is right"; STL recomputes per-face normals from winding — wrong winding = inverted normal.)

- **Test for:** render single-sided (backface culling on) from all sides — any face that disappears or goes black has reversed winding. For a closed mesh, every face normal points away from the centroid.
- **Failure mode — Inverted normals:** faces invisible/black from the intended side, lighting inside-out; fine in double-sided preview, broken in the real culled+lit pipeline.

## D.2 Keep meshes manifold and degenerate-free (watertight where required)

Manifold = every edge shared by exactly two faces, no fan-joining vertices. Reject/repair degenerate triangles (zero area — collinear/coincident verts) and degenerate UVs (UV triangle collapsed to a line/point). Require watertight (no open boundary edges) for collision/physics/boolean ops. ✅ (Non-manifold output is "less suitable for UV unwrapping, 3D printing, and physical simulations"; MC's duplicate vertices "make smooth shading impossible".)

- **Test for:** topology check — edge-manifold (≤2 faces/edge), area > ε, no NaN normals, no collapsed UV triangles, weld coincident vertices; for watertight, assert zero boundary edges.
- **Failure mode — Non-manifold / degenerate mesh:** breaks physics/collision unpredictably, blows up UV unwrap and lightmap bake, NaN normals render as black garbage — all invisible until a downstream system chokes.

## D.3 Generate off the main thread; cache; budget GPU uploads

Never regenerate static geometry per frame. Generate on worker threads, **cache** chunks (regenerate only on change), spread GPU mesh uploads across frames, and bound the worker queue so it can't outrun consumption. ✅ (In Godot voxel systems "the first GL call of a frame costs ~15 ms so the engine uploads ~one mesh per frame"; "loading chunks blocks the main thread" → stutter; unbounded queues "run out of memory".)

- **Test for:** profile during heavy generation — main-thread generation ≈ 0 (work on workers); no frame spikes from a synchronous regenerate or upload burst; moving through fresh terrain doesn't stutter.
- **Failure mode — Generation hitch / memory blow-up:** per-frame or main-thread regeneration stutters; unbounded threaded generation exhausts memory.

**→ Procedural / headless implication.** Caching + threading are *architectural requirements*, not optimizations — design the pipeline around "generate once, cache, regenerate on change" from the start (retrofitting is a rewrite). The topology checks above are the geometric analogue of game-feel's juice-budget linter: run on every generated mesh, **block commit on failure**.

---

# Sub-domain E — Vegetation & Scatter Systems

> Scatter is where "it's generated" shows fastest: a grid screams artificial, pure-random clumps and voids read as bugs. **Blue noise** for placement, **environmental masks** for ecology, **instancing** for scale. ✅

## E.1 Scatter with blue noise (Poisson-disc) — never grid or pure-random

Poisson-disc / blue-noise sampling keeps every instance ≥ a minimum distance from neighbors — the even-but-non-repeating spacing real vegetation has. ✅ (Pure random "clusters into dense piles and empty voids"; blue noise = "fine-scale variation without large-scale structure"; Bridson's O(n) algorithm; tiling sets sampled on a torus tile seamlessly.)

- **Test for:** from above — no rows/grid, no large bald patch abutting a dense clump, minimum spacing respected.
- **Failure mode — Grid tell or random clumping:** grid placement screams "generated"; random piles trees with gaps that read as bugs.

## E.2 Mask placement by slope, altitude, moisture, biome

Gate scatter by environmental masks: slope (no trees on cliffs), altitude (tree/snow line), moisture/biome (Whittaker temperature×moisture → biome → valid species), and a density field. Blue noise supplies candidates; masks decide which survive and what they become. ✅ (Whittaker classification used in Dwarf Fortress, Minecraft; biome systems combine altitude+precipitation+temperature.)

- **Test for:** no tree on a vertical slope or above the tree line; species matches biome (no cacti in tundra); density tracks the mask (dense valleys, sparse ridges); biome boundaries aren't hard unnatural lines.
- **Failure mode — Context-blind scatter:** trees on cliffs and underwater, jungle plants in snow, uniform "sprinkled everywhere" density.

## E.3 Instance at scale with GPU culling and indirect draws

Render dense scatter with GPU instancing — one draw call per mesh/material, transforms in a GPU buffer, frustum/distance culling on the GPU (compute), LOD/impostors for distant instances. Foliage is tiny-triangle, huge-count: instancing is mandatory. ✅ (Indirect instancing + GPU culling does culling "before rendering, faster and with no CPU cost"; non-indirect instanced draws cap ~1023/call.)

- **Test for:** draw calls ≈ (meshes × materials), not (instances); off-screen instances culled (GPU); far instances drop to LOD/impostor; CPU cost near-flat as instance count rises.
- **Failure mode — Per-instance draws / no culling:** one object per plant tanks the CPU; full detail off-screen and at distance tanks the GPU.

**→ Procedural / headless implication.** Min-distance, density, masks, seed are all safe parameters; precompute tiling blue-noise so scatter is cheap and seamless across chunks. Scatter output must be **instance references + transforms**, never bespoke per-plant geometry (the "compose from a vetted library" pattern again) — a generator emitting unique meshes per plant breaks the performance budget. Grid-aligned placement is a direct oatmeal failure; the validator should reject it.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Blobby "noise terrain" | Equal roughness, no ridgelines/drainage | Multifractal/ridged + domain warp + erosion (A.2/A.3/B.3) |
| Directional banding | Axis-aligned bands in lighting/normals | Simplex or warp the domain (A.1) |
| Cloudy sameness | Un-warped fBm reads as static cloud | Domain warping (A.3) |
| Disconnected caves | Sealed unreachable air pockets | Flood-fill connectivity validation (A.3) |
| Wrong representation | Voxels for flat terrain, or faked overhangs | Match representation to topology need (B.1) |
| Rounded sharp features | MC melts cube corners | Dual contouring for sharp features (B.2) |
| Non-manifold cells | DC leaks bad geometry to physics/UV | Manifold repair in the validator (B.2/D.2) |
| Un-eroded / over-eroded | Video-game-noise look, or flattened mush | Tuned hydraulic+thermal erosion bake (B.3) |
| Cracks & LOD pop | See-through seams, geometry jumps | Skirts/edge-matching; geomorph (B.4) |
| Impossible/repetitive buildings | Windows through corners, or all identical | Architectural constraints in the grammar (C.2) |
| Contradiction holes | Naive WFC leaves gaps | Backtrack/restart + connectivity check (C.3) |
| Grid tell / oatmeal structures | Reads as generated; 10k identical | Constraints + hand-placed anchors; oatmeal test (C.4) |
| Inverted normals | Black/invisible faces from one side | Consistent winding; normals from winding (D.1) |
| Degenerate mesh | NaN normals, broken collision/UV | Zero-area + collapsed-UV rejection (D.2) |
| Generation hitch | Stutter when new terrain loads | Worker threads + caching + upload budget (D.3) |
| Grid/clumped scatter | Visible rows, or piles + bald voids | Blue-noise (Poisson-disc) placement (E.1) |
| Context-blind scatter | Trees on cliffs, jungle in snow | Slope/altitude/biome masking (E.2) |
| Per-instance draws | CPU tanks with dense foliage | GPU instancing + GPU culling (E.3) |

---

## Caveats

- **"Better noise" isn't the lever; structure is.** Believability comes from multifractal modulation, domain warping, and *erosion* — not from Simplex-over-Perlin or more octaves. Noise-family choice matters only where its artifact (banding) shows.
- **WFC ≈ model synthesis.** The "wave function collapse" name (Gumin 2016) reimplements Merrell's model synthesis (2007); the quantum framing is branding, not new math. ⚠️ Cite Merrell as origin. Naive WFC has no completeness guarantee — it needs backtracking/restart.
- **Erosion realism vs. cost, and parameters don't transfer.** Droplet hydraulic erosion is reproduced from Lague's *teaching* implementation ⚠️; good parameters are scale/resolution-dependent and must be re-tuned per world, not copied.
- **Cave/overhang generation is under-constrained.** 3D-noise isosurface caves are mostly community-documented ⚠️; connectivity and reachability aren't guaranteed and must be validated, or the world fills with sealed voids.
- **The oatmeal problem is worse for geometry than the literature admits.** Most sources optimize *plausibility per instance*, not *variety across instances*. Uniform parametric variation of one formula (the No Man's Sky superformula critique, applied geometrically) yields mathematically-infinite, perceptually-identical output. The hybrid model + the `procgen-review` gate are the only reliable answers, and at full open-world scale this stays genuinely hard — de-risk with the oatmeal/fanfic tests early.
- **Topology validators catch correctness, not beauty.** A mesh can be manifold, watertight, correctly wound, and still ugly or unreadable. The validator is a floor (won't break downstream), not a ceiling (serves the design) — `level-design` and `art-direction-and-readability` own the ceiling.

---

*Expand with your own generation findings and citations. See CONTRIBUTING.md.*
