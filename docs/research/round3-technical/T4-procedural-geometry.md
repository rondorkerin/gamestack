# T4 — Procedural Geometry: Generated Meshes, Terrain & Structures Research

> **Research document** — seed for the `procedural-geometry` technical-craft skill.
> Written by the gamestack research agent; adversarially verified against independent sources.
> Verification tags: ✅ ≥2 independent/primary sources · ⚠️ single source · ❌ refuted or substantially contested.
>
> **Scope boundary (read first):** this document covers the *math and algorithms that produce geometry* —
> meshes, terrain, structures, scatter. It is **not** `procedural-generation` (content/narrative/lore
> coherence at scale) and **not** `level-design` (the legibility principles this geometry must serve).
> It is the geometry engine *underneath* those skills, and its output is gated by `procgen-review`'s
> oatmeal test for **geometric** sameness.

---

## 1. TL;DR

- **Noise is the substrate, but a single noise field is oatmeal.** Plain fractal Brownian motion (fBm) lacks the ridge-and-valley structure of real landforms and reads as repetitive lumps; the believability comes from *modulating* it — multifractal/ridged noise, domain warping, and erosion simulation — not from more octaves. ✅
- **Heightfields are 2.5D; voxels/SDFs are the only way to get true overhangs and caves.** A heightmap stores one height per (x,z) and physically cannot represent an overhang. Voxel/signed-distance-field terrain meshed with marching cubes or dual contouring can — at a large memory and complexity cost. Pick the cheaper representation unless the design *needs* 3D topology. ✅
- **"Looks designed, not random" is a constraint problem, not a randomness problem.** Grammar- and constraint-based methods (L-systems, shape grammars, wave function collapse / model synthesis) get their authored-looking results from *hand-authored rules and tilesets*, not from the algorithm — the designer's craft moves into the constraint set. ✅
- **Most procedural-mesh bugs are topology bugs, and they are silent.** Inverted normals (wrong winding order), non-manifold edges, degenerate (zero-area) triangles, and collapsed UVs produce meshes that *look* fine in one lighting condition and break in another. A generator cannot see them; a topology validator must. ✅
- **The whole topic is one big procedural implication.** Frame every technique by its authoring surface: which parameters a generator can vary safely (frequency, octaves, density masks) versus what must stay a hand-tuned constant or a hand-placed anchor (silhouette landmarks, the erosion/normal/budget contracts) — and validate geometric variation against the oatmeal test, because parametric variation of one formula produces perceptually identical results.

---

## 2. Key Findings

**1. Perlin noise is gradient noise; the value/Perlin/Simplex choice is a cost-vs-artifact tradeoff, not a quality ladder.** ✅
Ken Perlin introduced gradient noise in "An Image Synthesizer" (SIGGRAPH 1985), developed while working on *Tron* at MAGI, and won a 1997 Academy Award for Technical Achievement for it; "Improving Noise" (SIGGRAPH 2002) fixed second-order interpolation discontinuities and gradient bias. **Value noise** picks a pseudo-random value per lattice point and interpolates; **Perlin noise** interpolates a *dot product of gradient vectors*, giving smoother, more isotropic results; **Simplex noise** (Perlin 2001) uses a simplex lattice (triangle/tetrahedron) to cut directional artifacts and scale to higher dimensions cheaply. The performance win of Simplex over Perlin is large in 4D+ but "slighter for low dimensions" — so the choice is driven by *where directional banding shows up* (it appears in normals/specular response), not by an absolute ranking.

**2. Plain fBm looks fake because real terrain is multifractal; ridged/multifractal modulation and erosion are what sell it.** ✅
fBm sums octaves of noise with a frequency multiplier (lacunarity, typically ~2) and an amplitude falloff (persistence/gain, typically ~0.5); more octaves add detail but not *structure*. Musgrave ("Procedural Fractal Terrains," in Ebert et al., *Texturing & Modeling*) shows that real landscapes have *heterogeneous* fractal dimension — smooth sediment-filled valleys, rough stress-concentrated ridges — which plain fBm cannot reproduce, producing "really isolated peaks" with no ridge lines. Multifractal noise modulates each octave's amplitude by the previous octave's value so detail accumulates on highlands and smooths in lowlands; ridged multifractal (taking `1 - |noise|` per octave) creates sharp ridgelines. Erosion simulation closes the rest of the gap.

**3. Domain warping — `f(g(p))` — is the cheapest single technique that makes noise look organic instead of cloudy.** ✅
Inigo Quilez's canonical formulation distorts the input domain before evaluation: `g(p) = p + h(p)`, so you evaluate `fbm(p + fbm(p + fbm(p)))`. The nested warp introduces flow, swirl, and self-similar river/marble structure that no amount of octave-stacking on an un-warped field produces. The intermediate warp fields (Quilez's `q` and `r`) are reusable as masks for coloring or biome assignment. It is a few extra noise evaluations — cheap relative to its visual payoff.

**4. Heightfield terrain is 2.5D by construction; only a volumetric representation can encode overhangs and caves.** ✅
A heightmap is a function height(x, z) — one value per column — so it can express hills and valleys but is *mathematically incapable* of an overhang, arch, or cave (which require two surfaces over the same column). Volumetric terrain stores a 3D scalar field (occupancy or signed distance) and extracts a mesh from it; caves are commonly carved by thresholding a 3D noise isosurface (e.g. keep where 3D noise ≈ 0 ± 0.1). The cost is steep: a 3D field is memory-heavy and needs a mesh-extraction step every time it changes.

**5. Marching cubes and dual contouring are the two standard isosurface extractors, and they fail differently.** ✅
Marching cubes (Lorensen & Cline, SIGGRAPH 1987 — the most-cited SIGGRAPH paper of all time) places vertices on cube *edges*, so it cannot represent sharp features and emits large numbers of duplicate/near-duplicate vertices that defeat smooth shading; chunked MC terrain needs a stitching scheme (Transvoxel) to avoid LOD seams. Dual contouring (Ju, Losasso, Schaefer & Warren, SIGGRAPH 2002) places one vertex *inside* each cell using Hermite data (surface value **and** gradient), so it preserves sharp corners and natively joins octree nodes of different sizes — making LOD far easier — at the cost of occasionally producing non-manifold output. Choose MC for simplicity and smooth blobs; DC for sharp features and easier LOD.

**6. Erosion simulation is the difference between "noise terrain" and "terrain."** ✅
Hydraulic erosion (Sebastian Lague's widely-referenced "Coding Adventure: Hydraulic Erosion" and open-source implementation) simulates rainfall as individual droplets that trace downhill, picking up and depositing sediment, carving connected drainage networks and V-shaped valleys that noise never produces; thermal erosion collapses slopes steeper than a material's talus angle into scree. These are the features the eye reads as "this was shaped by a process." Erosion is an expensive *offline/bake* step, not a per-frame one.

**7. Grammar- and constraint-based structure generation moves the design craft into the rule/tileset, not the algorithm.** ✅
L-systems (Lindenmayer, 1968) rewrite strings via production rules interpreted as turtle-graphics commands — excellent for self-similar organic growth (plants, trees, branching). Shape grammars / CGA (Parish & Müller, "Procedural Modeling of Cities," SIGGRAPH 2001; later ESRI CityEngine) split a mass into floors, facades, and tiles via parameterized rules — excellent for buildings and cities. Wave function collapse (Maxim Gumin, 2016) is a near-identical reimplementation of Paul Merrell's earlier **model synthesis** (2007 i3D / 2009 thesis): a constraint solver that places tiles consistent with authored adjacency rules. In every case the "designed" quality comes from the authored grammar/tileset — the algorithm only enforces it.

**8. Townscaper and Bad North prove that "designed-looking" output is an artifact of hand-crafted constraints, not the solver.** ✅
Oskar Stålberg's *Townscaper* runs WFC + marching cubes on an *irregular* grid (a custom half-edge mesh) so paths and shapes curve organically instead of reading as a voxel grid; *Bad North* computes tile adjacency constraints on import and uses a navigability heuristic so every generated island is playable. The craft is in the tileset and the constraints — large, carefully modeled tiles give Bad North its "smoothly curving beaches and chunky houses." Same algorithm, generic tileset = generic output.

**9. The dominant procedural-mesh defects are silent topology bugs.** ✅
Inverted normals come from inconsistent triangle winding order (front faces should wind consistently — typically counter-clockwise — because the normal is derived from winding by the right-hand rule); they render fine from one side and invisible/black from the other. Non-manifold geometry (an edge shared by >2 faces, or a vertex joining two otherwise-separate fans) breaks UV unwrapping, physics, and collision. Degenerate triangles (zero area — three collinear or coincident vertices) and degenerate UVs (a triangle's UVs collapsed to a line/point) produce NaN normals and broken texturing. None of these are visible to the generator; all are mechanically checkable.

**10. Runtime mesh generation is a frame-time hazard, and the fix is threading + caching, not faster generation.** ✅
Regenerating geometry on the main thread blocks the frame and causes stutter; uploading a freshly generated mesh to the GPU is itself costly (the first GL call of a frame can cost ~15 ms, so engines throttle to roughly one mesh upload per frame). The standard mitigations are: generate on worker threads, *cache* generated chunks and only regenerate on change, budget mesh uploads across frames, and never regenerate static geometry per frame. Memory back-pressure is the failure mode if worker threads outrun consumption.

**11. Uniform/grid scatter is an instant "it's generated" tell; blue-noise (Poisson-disc) distribution is the fix.** ✅
Pure random placement produces white noise — visible clumps and bald voids — while a regular grid reads as obviously artificial. Poisson-disc sampling (Bridson's O(n) algorithm, 2007) produces *blue noise*: every point at least a minimum distance from its neighbors, giving the even-but-non-repeating spacing that natural vegetation actually has. Placement is then masked by slope, altitude, moisture/biome (Whittaker temperature×moisture classification), and density fields; at scale, GPU instancing with indirect draws and GPU culling renders the result in roughly one draw call per mesh/material.

---

## 3. Details by Sub-domain

---

### Sub-domain A — Noise as the Geometry Substrate

**Foundation:** Ken Perlin, "An Image Synthesizer" (SIGGRAPH 1985) and "Improving Noise" (SIGGRAPH 2002); Musgrave, "Procedural Fractal Terrains" in Ebert, Musgrave, Peachey, Perlin & Worley, *Texturing & Modeling: A Procedural Approach*; Inigo Quilez, "domain warping" and "fbm" articles.

#### A.1 Pick the noise family by its artifact, not its reputation

**Rule:** Use **value noise** for cheap, smooth, low-stakes variation; **Perlin/gradient noise** as the general-purpose default; **Simplex** when directional banding shows up (it surfaces in normals, specular, and ice/glossy response) or when you need 4D (e.g. animating 3D noise over time). Do not assume Simplex is "better" — in 2D/3D its speed edge is small.

- **Exemplar:** Perlin noise drove natural surface texture from *Tron* (1982) onward and earned a 1997 Academy Award for Technical Achievement; "Improving Noise" (2002) corrected the lattice-boundary discontinuity that made the original visible in derivatives. ✅
- **Test for:** render the noise as a normal map / lit surface, not just a grayscale heightmap. Look for axis-aligned banding or a visible grid; if present and it matters, switch to Simplex or rotate/warp the domain. Confirm the noise is seeded deterministically (same seed → same field) for reproducibility.
- **Failure mode — Directional banding / grid tell:** Perlin's grid-derived gradients produce faint axis-aligned bands, invisible in a flat gray preview but obvious once the field drives lighting.

**→ Procedural / headless implication.** Noise type, seed, frequency, and dimension are all safe generator parameters — but the *seed must be recorded* so a flagged-bad region can be reproduced and re-rolled. A generator that loses its seeds cannot be reviewed or repaired.

#### A.2 Build terrain from fBm, then break fBm's uniformity

**Rule:** Stack octaves as fBm (lacunarity ≈ 2.0, gain ≈ 0.5 as the default starting point), then *modulate* — switch to multifractal (amplitude of each octave scaled by the running value) for heterogeneous roughness, or ridged multifractal (`1 - |noise|` per octave) for sharp mountain ridgelines. Plain fBm alone is the canonical "fake terrain" look.

- **Exemplar / source:** Musgrave's multifractal terrains demonstrate that real landscapes have *non-constant* fractal dimension — smooth valleys, rough ridges — which plain fBm cannot express, yielding "isolated peaks" with no ridge structure; ridged multifractal recreates ridge-and-valley distribution with no physics, "it just emerges from the mathematical structure." ✅
- **Test for:** generate a heightfield with plain fBm and with ridged multifractal at equal octave count; the plain version has rounded blobby peaks and no connected ridgelines. A believable range must show continuous ridge lines and valleys, not a field of bumps.
- **Failure mode — Blobby/uniform "noise terrain":** equal roughness everywhere; no drainage, no ridgelines; reads as a grayscale-heightmap demo, not a place.

**→ Procedural / headless implication.** Lacunarity, gain, octave count, and the fBm-vs-multifractal choice are per-biome parameters a generator can vary safely. But the *parameter ranges* that read as "mountains" vs "plains" vs "badlands" should be hand-authored presets, not a continuous slider the generator free-rolls — most of that continuous space looks like oatmeal.

#### A.3 Use domain warping for organic flow and Worley/cellular noise for cells and caves

**Rule:** Apply domain warping (`f(p + fbm(p))`, nested 1–2 levels) to introduce flow and swirl — rivers, marble, wind-carved rock — that octave-stacking cannot. Use Worley/cellular noise for cell-like patterns (cracked ground, stone, scales) and 3D noise isosurfaces for caves (carve where 3D noise ≈ 0 ± ε; lower frequency → larger caverns, higher → vein-like tunnels).

- **Exemplar / source:** Quilez's domain-warping article: "Warping simply means we distort the domain with another function g(p) before we evaluate f," producing "abstract but beautiful images with a pretty organic quality"; the intermediate warp fields double as free coloring/masking inputs. ✅ Cave-by-3D-noise-isosurface is the standard volumetric-cave technique. ⚠️ (well-documented community technique; fewer formal sources)
- **Test for:** a warped field shows directional flow (riverlike channels, swirls) absent from the un-warped field at the same octave count. For caves, confirm the carved volume is *connected* (no orphan floating air pockets) and meets the surface somewhere (reachable).
- **Failure mode — Cloudy sameness / disconnected caves:** un-warped fBm reads as static cloud; naive 3D-noise caves produce isolated unreachable pockets and Swiss-cheese walls.

**→ Procedural / headless implication.** Warping is cheap and parametric — safe to expose. Cave connectivity and reachability are *not* guaranteed by the noise and must be validated (flood-fill from the surface) before the cave is shipped, or the generator will produce sealed, pointless voids.

---

### Sub-domain B — Terrain Generation

Covers: heightfield vs voxel/SDF, mesh extraction (marching cubes / dual contouring), erosion, and LOD/chunking at scale.

#### B.1 Default to heightfields; reach for voxels/SDF only when you need true 3D

**Rule:** Use a heightmap (one height per x,z cell) unless the design *requires* overhangs, arches, or caves — heightfields are vastly cheaper in memory, simpler to LOD, trivially collidable, and easy to author. Move to a voxel/SDF representation only for genuine 3D topology, and accept the memory and meshing cost.

- **Exemplar:** the heightfield is the dominant terrain representation across shipped engines precisely because it is cheap and 2.5D is enough for most landscapes; No Man's Sky's GDC 2017 "Continuous World Generation" (Innes McKendrick) uses a voxel pipeline specifically because its worlds need caves, arches, and overhangs. ✅
- **Test for:** does any design requirement actually need two surfaces over one column (overhang/cave/arch)? If no, a heightfield is correct and a voxel system is over-engineering. If yes, a heightfield physically cannot do it — no parameter tuning fixes a 2.5D representation.
- **Failure mode — Wrong representation for the requirement:** shipping voxels for flat-ish terrain (paying huge cost for unused capability), or trying to fake overhangs on a heightfield (impossible; produces vertical-wall artifacts).

**→ Procedural / headless implication.** The representation is an *architecture* decision, not a generator parameter — fix it once, up front, from the design's topology needs. A generator cannot switch representations mid-world.

#### B.2 Choose the isosurface extractor by sharp-feature need and LOD strategy

**Rule:** For volumetric terrain, extract the mesh with **marching cubes** when surfaces are smooth/blobby and you want the simplest, fastest, best-documented path; with **dual contouring** when you need sharp edges/corners (cut cliffs, structures) or want native multi-resolution LOD. Budget for MC's seam-stitching (Transvoxel) and duplicate-vertex problem.

- **Exemplar / source:** Marching cubes (Lorensen & Cline 1987) places vertices on cube edges — cannot model sharp parts, emits duplicate vertices that block smooth shading, and needs Transvoxel to stitch chunk LODs. Dual contouring (Ju, Losasso, Schaefer & Warren 2002) places a vertex inside each cell from Hermite data, preserving sharp features and joining differently-sized octree leaves without special handling. ✅
- **Test for:** extract a known sharp shape (a cube corner). MC rounds it; DC preserves it. Inspect for duplicate vertices (MC) and non-manifold edges (DC) — each algorithm's characteristic defect.
- **Failure mode — Rounded-off sharp features (MC) / non-manifold cells (DC):** picking MC for architecture loses every corner; picking DC without manifold-checking leaks non-manifold geometry into physics and UV.

**→ Procedural / headless implication.** Extractor choice is fixed per project. Whichever you choose, its characteristic defect must be in the topology validator (MC → weld duplicate vertices; DC → detect/repair non-manifold output) before the mesh is committed.

#### B.3 Run erosion to add the structure noise cannot

**Rule:** After generating a base heightfield, run hydraulic erosion (droplet/sediment simulation) for drainage networks and V-valleys, and thermal erosion (talus-angle slumping) for realistic scree slopes. Bake it offline — erosion is iterative and far too expensive per frame.

- **Exemplar / source:** Sebastian Lague's "Coding Adventure: Hydraulic Erosion" (open-source, MIT) simulates rainfall "one droplet at a time, tracing its path downhill and picking up and depositing sediment," producing convincing landscapes; the technique is widely reproduced. Erosion adds the connected drainage and ridge-to-valley sediment grading that fBm and even multifractal noise lack. ✅
- **Test for:** after erosion, water-flow should be *connected* (ridgelines shed into valleys into a drainage network), slopes should not exceed the material talus angle, and valleys should show sediment smoothing. Compare pre/post: erosion visibly carves channels.
- **Failure mode — Un-eroded noise terrain / over-eroded mush:** no erosion = video-game-noise look; too many iterations = everything flattened to rolling hills with no drama. Tune iteration count and deposition rate.

**→ Procedural / headless implication.** Erosion is a *bake* a generator runs once per region and caches — never a runtime cost. Its parameters (droplet count, erosion/deposition rate, talus angle) are safe to vary per biome; the result must be cached because re-running it on load would stall.

#### B.4 LOD and chunk the terrain, and fill the cracks

**Rule:** Stream terrain as chunks with level-of-detail proportional to camera distance — quadtree chunked LOD or geometry clipmaps for heightfields, octree for voxels. At LOD boundaries, prevent visible cracks/seams with **skirts** (vertical aprons hiding the gap) or edge-matching (Transvoxel for MC, native for DC).

- **Exemplar / source:** chunked LOD uses a quadtree of chunks with skirts "to fill cracks rather than force each chunk edge to match"; geometry clipmaps stream small heightmap patches at constant GPU cost with lightweight CPU-side quadtree traversal; spherical clipmaps extend this to planets. ✅
- **Test for:** at any LOD transition, look along the seam at a grazing angle — no gap to the skybox should be visible (a "crack"), and no sudden geometry jump ("pop") that isn't masked. CPU-side LOD selection should be cheap (a few hundred node tests), not a per-vertex cost.
- **Failure mode — Cracks and LOD pop:** mismatched chunk-edge resolutions open hairline gaps you can see through; abrupt LOD swaps pop visibly. Skirts hide cracks; geomorphing or dithered transitions hide pop.

**→ Procedural / headless implication.** Chunk size, LOD distances, and skirt depth are tunable, but the *crack-free contract* is non-negotiable and validator-checkable: generate adjacent chunks at different LODs and assert no through-gap at the boundary. A generator that emits cracked seams has produced malformed terrain.

---

### Sub-domain C — Structural & Architectural Generation

Covers: L-systems (organic), shape grammars (architecture), wave function collapse / model synthesis (tiles), constraint-based layout, and the "looks designed, not random" test. This is the geometry engine *underneath* `level-design`'s legibility principles.

#### C.1 Use L-systems for self-similar organic growth

**Rule:** Use L-systems (string-rewriting production rules interpreted as turtle commands) for branching, self-similar organic forms — trees, plants, vines, coral, river networks. Use bracketed/stochastic L-systems for branching and variation; do not reach for L-systems for rectilinear architecture (the wrong tool).

- **Exemplar / source:** Lindenmayer (1968) formalized L-systems for modeling growth; an alphabet + production rules + axiom generate a string interpreted by a turtle (forward, turn, push/pop orientation) into 3D plant geometry. Standard in vegetation generation. ✅
- **Test for:** the same ruleset with different seeds/stochastic rules yields *recognizably the same species* with individual variation (different branch counts/angles), not random scribbles. A good tree ruleset reads as "an oak," varied.
- **Failure mode — Fractal-obvious or chaotic output:** too-regular rules read as an obvious fractal; unconstrained stochastic rules read as chaotic non-plants. Bias toward botanical constraints (gravity, phototropism, branch tapering).

**→ Procedural / headless implication.** Rules and angle/length parameters are the authoring surface; expose ranges, not the whole space. The ruleset *is* the species identity — a small library of hand-authored rulesets, varied per instance, beats one free-rolling grammar (which produces oatmeal trees).

#### C.2 Use shape grammars for architecture; the authored grammar is the building's identity

**Rule:** Generate buildings and cities with shape grammars / CGA — start from a mass, split into floors and facades, subdivide facades into tiles (windows, doors), via parameterized production rules. Keep architectural constraints (floor alignment, symmetry, structural plausibility) in the grammar.

- **Exemplar / source:** Parish & Müller, "Procedural Modeling of Cities" (SIGGRAPH 2001) and the later CGA shape grammar → ESRI CityEngine: parameterized rules generate buildings, "refining details through grammar productions," with pseudo-random values for variation; Wonka et al., "Instant Architecture" (2003) is a parallel approach. ✅ Noted limitation: rule-based output "often lacks diversity and realism" without careful authoring.
- **Test for:** generated buildings obey real architectural logic — floors align, windows are regularly spaced and don't clip corners, roofs cap cleanly, scale is human-plausible (door height ≈ human). A façade should not have a window bisected by a floor slab.
- **Failure mode — Architecturally impossible / repetitive buildings:** windows through corners, floating floors, doors to nowhere; or, oppositely, every building visibly identical because the grammar has too little variation.

**→ Procedural / headless implication.** The grammar is hand-authored craft; the generator varies parameters within it. This is exactly where `level-design`'s legibility principles bind the geometry: the grammar must produce *readable* structures (clear entrances, navigable interiors), not just plausible facades — geometry serves legibility, it doesn't replace it.

#### C.3 Use WFC / model synthesis for constraint-consistent tile assembly

**Rule:** Use wave function collapse / model synthesis when you have a set of tiles with **authored adjacency rules** and want a large layout where every local junction is valid — dungeons, buildings, levels, towns. Expect to spend most of your effort on the tileset and constraints, and to add backtracking or a navigability heuristic because naive WFC can paint itself into contradictions.

- **Exemplar / source:** Gumin's WFC (2016) is a near-identical reimplementation of Merrell's **model synthesis** (2007 i3D, 2009 thesis); shipped in *Bad North*, *Townscaper*, *Caves of Qud*. Stålberg computes tile adjacency on import and, for *Bad North*, "uses a heuristic that tries to select tiles such that the resulting zone is navigable at each step." ✅
- **Test for:** every tile junction in the output satisfies the adjacency rules (no illegal neighbors), the result is *connected/navigable* where it must be, and the generator handles contradictions (backtrack or restart) rather than emitting a broken cell. Run many seeds: do they read as varied, or as the same tileset shuffled?
- **Failure mode — Contradiction/holes and tileset-shuffle sameness:** naive WFC hits unsatisfiable cells and leaves holes; and with a small generic tileset, every output looks like the same pieces rearranged (geometric oatmeal).

**→ Procedural / headless implication.** The tileset and adjacency constraints are the entire craft surface — author them, and the algorithm enforces. Add a **navigability/connectivity validator** to the loop (WFC does not guarantee reachability). This is the most direct lever on the oatmeal test: variety comes from tileset richness and constraint design, not from running the solver more.

#### C.4 Make "looks designed, not random" a constraint, and anchor with hand-placed set pieces

**Rule:** The "designed" quality is produced by constraints and by **hand-authored anchors**, not by the generator. Hybridize: hand-place the landmarks and key structures (the silhouette beats, the boss arena, the named town); let the generator fill between them under constraints. Townscaper's irregular grid and Bad North's large hand-modeled tiles are the lesson — invest in the authored pieces.

- **Exemplar / source:** *Townscaper* runs WFC + marching cubes on a custom irregular (half-edge) grid so output curves organically instead of reading as a voxel grid; *Bad North*'s "large tiles give smoothly curving beaches, chunky houses and extra cliff variation." Same algorithm with a generic grid/tileset = generic output. ✅
- **Test for:** apply `procgen-review`'s oatmeal test to a batch of generated structures — are they *perceptually* distinct, or mathematically-different-but-visually-same? If the latter, the fix is richer constraints/tilesets and hand-placed anchors, not more randomness.
- **Failure mode — Grid tell / oatmeal structures:** regular-grid output reads as obviously generated; uniform parametric variation of one template produces 10,000 perceptually-identical buildings.

**→ Procedural / headless implication.** This is the crux: a generator *cannot* author taste. The hybrid (hand-authored anchors + constrained fill) is the only reliable path to designed-looking geometry at scale, and the oatmeal test is the gate. Cross-reference `procedural-generation` (the content-level statement of this same hybrid) — here it is the *geometric* statement.

---

### Sub-domain D — Mesh Generation & Topology Correctness

Covers: watertight/manifold meshes, winding/normals, degenerate geometry and UVs, and runtime generation performance/caching.

#### D.1 Wind triangles consistently so normals point outward

**Rule:** Emit every triangle with a consistent winding order (pick one — typically counter-clockwise front faces) so the right-hand-rule normal points *outward*. Compute or assign normals from winding deliberately; do not trust whatever the generation order happened to produce.

- **Exemplar / source:** procedural meshes "commonly encounter problems with normals even when the mesh itself appears to have the correct shape"; STL recomputes per-face normals from winding via the right-hand rule, so wrong winding = inverted normal. ✅
- **Test for:** render the mesh single-sided (backface culling on) from all sides — any face that disappears or renders black has reversed winding. Programmatically: for a closed mesh, every face normal should point away from the centroid.
- **Failure mode — Inverted normals:** faces invisible/black from the intended side, lighting inside-out; looks fine in double-sided preview, breaks in the real (culled, lit) pipeline.

**→ Procedural / headless implication.** Winding is a hard contract the generator must obey on every emitted triangle, and it is cheaply validated — make normal-consistency a blocking check.

#### D.2 Keep generated meshes manifold and free of degenerate geometry

**Rule:** Ensure generated meshes are manifold (every edge shared by exactly two faces, no fan-joining vertices) and watertight where required (collision, physics, 3D printing, boolean ops). Reject or repair degenerate triangles (zero area — collinear/coincident vertices) and degenerate UVs (UV triangle collapsed to a line/point).

- **Exemplar / source:** degenerate faces are "zero-area triangles where all three vertices lie on a line or at the same point," common in noisy generation; non-manifold results are "less suitable for UV unwrapping, 3D printing, and physical simulations"; marching cubes' duplicate vertices make "smooth shading impossible." ✅
- **Test for:** run a topology check — edge-manifold (≤2 faces per edge), no zero-area triangles (area > ε), no NaN normals, no collapsed UV triangles, weld coincident vertices. For watertight requirements, assert no boundary (open) edges.
- **Failure mode — Non-manifold / degenerate mesh:** breaks physics and collision unpredictably, blows up UV unwrap and lightmap bake, produces NaN normals that render as black/garbage. All invisible until the downstream system chokes.

**→ Procedural / headless implication.** This entire section is the **mesh topology validator** — the geometric analogue of game-feel's juice-budget linter. It runs on every generated mesh and blocks commit on failure. A generator has no eyes; topology correctness must be machine-enforced.

#### D.3 Generate off the main thread, cache aggressively, and budget GPU uploads

**Rule:** Never regenerate static geometry per frame. Generate on worker threads, cache generated chunks and regenerate only on change, and spread GPU mesh uploads across multiple frames. Bound worker output so it can't outrun consumption and exhaust memory.

- **Exemplar / source:** in Godot voxel systems, "the first call to OpenGL during the frame appears to take 15 ms on the CPU, and the voxel engine immediately stops uploading meshes," so typically one mesh uploads per frame; "when loading chunks, the main thread is blocked," causing stutter; unbounded worker queues "keep accumulating and make the game run out of memory." ✅
- **Test for:** profile a frame during heavy generation — main-thread generation cost should be ~0 (work is on workers); no frame should spike from a synchronous regenerate or a burst of mesh uploads. Moving the camera through fresh terrain should not stutter.
- **Failure mode — Generation hitch / memory blow-up:** per-frame or main-thread regeneration causes visible stutter; unbounded threaded generation exhausts memory.

**→ Procedural / headless implication.** Caching and threading are architectural requirements, not optimizations. The generation pipeline must be designed around "generate once, cache, regenerate on change" from the start — retrofitting threading onto a per-frame generator is a rewrite.

---

### Sub-domain E — Vegetation & Scatter Systems

Covers: blue-noise/Poisson-disc distribution, density/slope/biome masking, and instancing at scale.

#### E.1 Scatter with blue noise (Poisson-disc), never a grid or pure random

**Rule:** Place scattered objects (trees, rocks, grass clumps, props) with Poisson-disc / blue-noise sampling so every instance is at least a minimum distance from its neighbors — the even-but-non-repeating spacing real vegetation has. Never use a regular grid (obvious tell) or pure uniform random (clumps and bald patches).

- **Exemplar / source:** pure random "clusters, with dense piles and conspicuous empty voids"; blue noise has "energy concentrated in high frequencies, meaning fine-scale variation without large-scale structure"; Bridson's algorithm (2007) generates maximal Poisson-disc blue noise in O(n); tiling blue-noise sets sampled on a torus tile without seams. ✅
- **Test for:** view the scatter from above — no visible rows/grid, no large bald patches abutting dense clumps, minimum spacing respected. A grid is obvious at a glance; clumping is obvious as voids.
- **Failure mode — Grid tell or random clumping:** grid placement screams "generated"; random placement piles trees in clumps with empty gaps that read as bugs.

**→ Procedural / headless implication.** Min-distance, density, and seed are safe generator parameters; precompute tiling blue-noise sets so scatter is cheap and seamless across chunks. The grid-tell is a direct oatmeal failure for scatter — the validator should reject grid-aligned placement.

#### E.2 Mask placement by slope, altitude, moisture, and biome

**Rule:** Gate scatter by environmental masks: slope (no trees on cliffs), altitude (tree line, snow line), moisture/biome (Whittaker temperature×moisture → biome → valid species), and a density field. The placement candidate set comes from blue noise; the masks decide which candidates survive and what they become.

- **Exemplar / source:** the Whittaker diagram divides terrain by temperature and moisture into biomes (used in Dwarf Fortress, Minecraft) to decide "what plants and terrain features should be created"; biome systems combine "altitude, precipitation, and temperature," e.g. high-elevation/high-moisture/low-temp → snow, ocean-level/high-moisture/high-temp → rainforest. ✅
- **Test for:** no tree on a near-vertical slope, none above the authored tree line, species matches the biome (no cacti in tundra), and density tracks the mask (denser in fertile valleys, sparse on ridges). Spot-check biome boundaries for hard, unnatural lines.
- **Failure mode — Context-blind scatter:** trees on cliff faces and underwater, jungle plants in snow, uniform density ignoring terrain — the "sprinkled everywhere" look that breaks ecological believability.

**→ Procedural / headless implication.** Masks are the cheap, high-leverage authoring surface — most of the "this world has ecology" read comes from good masks, not from better tree models. They are fully parametric and safe to expose; author biome×slope×altitude rules as presets.

#### E.3 Instance at scale with GPU culling and indirect draws

**Rule:** Render dense scatter with GPU instancing — one draw call per mesh/material combination, transforms in a GPU buffer, frustum/distance culling on the GPU (compute shader), and LOD/impostors for distant instances. Foliage is tiny-triangle, huge-count: instancing is mandatory at scale.

- **Exemplar / source:** "foliage meshes tend to be very small in tri-counts but needed in huge numbers," making instancing ideal; indirect instancing (e.g. DrawMeshInstancedIndirect) plus GPU frustum culling via compute shaders does culling "before rendering the instances, both faster and not taking any CPU time," enabling "dense vegetation with very high frame rates." ✅ Non-indirect instanced draws cap at ~1023 instances per call.
- **Test for:** profile with dense scatter — draw calls should be ~(meshes × materials), not ~(instances); off-screen instances should be culled (GPU), and far instances should drop to LOD/impostor. CPU cost per frame should be near-flat as instance count rises.
- **Failure mode — Per-instance draw calls / no culling:** drawing each plant as its own object tanks the CPU; rendering off-screen and distant instances at full detail tanks the GPU.

**→ Procedural / headless implication.** Instancing is the contract that makes blue-noise scatter affordable; a generator that emits unique meshes per plant (instead of instanced references to a small library) breaks the performance budget. Scatter output must be *instance references + transforms*, not bespoke geometry — mirroring the "compose from a vetted library" pattern throughout this skill.

---

## 4. Recommendations

**Stage 1 — Choose representations and author the contracts (before any generation):**
1. Decide heightfield vs voxel/SDF from the design's topology needs (does it need caves/overhangs?). This is architectural and fixed; do not defer it.
2. Pick the isosurface extractor (MC vs DC) if volumetric, and commit to handling its characteristic defect (MC duplicate-vertex weld + Transvoxel seams; DC non-manifold repair).
3. Author the **mesh topology validator** (winding/normals, manifold, no degenerates, no collapsed UVs, weld threshold, watertight where required) — this is the non-negotiable backstop.
4. Author noise/erosion/biome presets as *hand-tuned parameter ranges* (mountains/plains/badlands; species rulesets; biome×slope×altitude masks), not free-rolling continuous sliders.

**Stage 2 — Generate the substrate (terrain):**
5. Build base terrain from fBm, then modulate (multifractal/ridged) and **domain-warp**; never ship plain fBm.
6. Run hydraulic + thermal **erosion as an offline bake**, cache the result, validate connected drainage and talus-respecting slopes.
7. Chunk + LOD the terrain; enforce the crack-free contract (skirts/edge-matching) with a boundary validator.

**Stage 3 — Generate structures:**
8. Use L-systems for organic growth, shape grammars for architecture, WFC/model synthesis for constrained tile assembly — investing the effort in rulesets/tilesets/constraints, not the algorithm.
9. **Hybridize:** hand-place silhouette landmarks and set pieces; let the generator fill between them under constraints.
10. Add a navigability/connectivity validator (WFC does not guarantee reachability; caves don't guarantee connection).

**Stage 4 — Scatter and instance:**
11. Scatter with tiling blue-noise (Poisson-disc); mask by slope/altitude/moisture/biome; never grid or pure-random.
12. Render via GPU instancing with GPU culling + LOD/impostors; emit instance references, never bespoke per-plant meshes.

**Stage 5 — Gate against geometric oatmeal:**
13. Run `procgen-review`'s oatmeal test on batches of generated terrain regions, structures, and scatter — perceptual distinctness, not mathematical. Route sameness failures back to richer constraints/tilesets/anchors, not more randomness.

**Thresholds that change the plan:**
- **No caves/overhangs needed →** drop voxels/SDF/MC/DC entirely; heightfield + erosion + scatter is the whole pipeline (vastly simpler).
- **Web/mobile target →** tighten octave counts, instance counts, and LOD distances hard; erosion and meshing must be baked offline, never runtime; favor heightfields.
- **Sharp architecture is central →** dual contouring or mesh-based (grammar) generation over marching cubes; MC will round every corner.
- **Hand-authored (not procedural) →** keep the topology validator (humans make winding/manifold mistakes too); drop the oatmeal-batch gate.
- **Truly infinite/streaming world →** seed determinism and chunk caching become load-bearing; every region must be reproducible from its seed for review and repair.

---

## 5. Caveats

- **"Better noise" is not the lever; structure is.** It is tempting to chase Simplex-over-Perlin or more octaves; the believability gains come from multifractal modulation, domain warping, and *erosion* — process, not noise type. The noise-family choice matters only where its artifact (banding) shows.
- **WFC ≈ model synthesis, and the naming is contested.** The popular "wave function collapse" name (Gumin 2016) post-dates and reimplements Merrell's model synthesis (2007); the quantum-mechanics framing is evocative branding, not new math. ⚠️ Cite Merrell as the origin to be accurate. Naive WFC also has no completeness guarantee — it can hit unsatisfiable contradictions and needs backtracking/restart.
- **Erosion realism vs. cost is a real tradeoff, and parameters don't transfer.** Droplet-based hydraulic erosion is widely reproduced from Lague's teaching implementation ⚠️ (a synthesis/teaching source, not a peer-reviewed method); good-looking parameters are scale- and resolution-dependent and must be re-tuned per world, not copied.
- **Cave/overhang generation is under-constrained.** 3D-noise isosurface caves are documented mostly in community sources ⚠️; connectivity and reachability are *not* guaranteed by the noise and must be validated separately, or the world fills with sealed pointless voids.
- **The oatmeal problem is worse for geometry than the literature admits.** Most procedural-geometry sources optimize for *plausibility per instance*, not *perceptual variety across instances*. Uniform parametric variation of one formula/template (the No Man's Sky superformula critique, applied geometrically) yields mathematically-infinite, perceptually-identical output. The hybrid hand-anchor + constrained-fill model and the `procgen-review` gate are the only reliable answers, and at full open-world scale this remains genuinely hard — de-risk with the oatmeal/fanfic tests early.
- **Topology validators catch correctness, not beauty.** A mesh can be perfectly manifold, watertight, correctly wound, and still ugly or unreadable. The validator is a floor (geometry won't break downstream), not a ceiling (geometry serves the design) — `level-design` and `art-direction-and-readability` own the ceiling.

---

## Relationship to Sibling Skills

**This skill owns geometry-generation math; it is deliberately narrow.** Four boundaries:

- **vs. `procedural-generation` (technique module):** that skill owns *content/narrative coherence at scale* — the oatmeal problem for quests, lore, items, history; voice-consistent corpora; tying generation to meaning. `procedural-geometry` owns the *mesh/terrain/structure math* underneath. The shared idea is the hybrid (hand-authored anchors + constrained fill) and the oatmeal test — stated here in *geometric* terms (silhouette landmarks, tilesets, blue-noise scatter) rather than narrative terms. When the question is "is this generated *content* meaningful," that's `procedural-generation`; when it's "is this generated *geometry* correct and varied," that's here.
- **vs. `level-design` (universal craft):** that skill owns the *legibility, wayfinding, gating, and pacing* of a level. `procedural-geometry` is the **geometry engine underneath it** — shape grammars and WFC produce the rooms and structures that `level-design`'s principles must then make readable and navigable. Geometry serves legibility; it does not replace it. A grammar that produces architecturally-plausible-but-illegible buildings has failed `level-design`'s test even if every triangle is correct.
- **vs. `procgen-review` (process):** that skill is the **gate** — its oatmeal test (perceptual sameness) and intentionality/anti-pattern gates run on the geometry this skill generates. The mesh topology validator here is the *correctness* floor; `procgen-review` is the *quality/variety* gate above it. Both must pass.
- **vs. the other technical-craft skills:** `3d-graphics-and-rendering` consumes the meshes this skill emits (LOD, culling, draw-call budgets are shared concerns); `shaders-and-vfx` textures them; `animation-systems` is unrelated (motion, not geometry). Where this skill says "instance, don't emit bespoke meshes," that is the same composition-from-a-vetted-library pattern that runs through `game-feel-and-juice`'s feedback budget and `shaders-and-vfx`'s material recipes.

**Practical rule for the AI agent:** to generate a world, generate *geometry* with this skill (correct, varied, performant), make it *legible* with `level-design`, give it *meaning* with `procedural-generation`/`worldbuilding-and-lore`, and *gate* the result with `procgen-review`. This skill guarantees the triangles are right and the variation is real; the others guarantee the place is worth being in.

---

## Sources

Primary / foundational:

- Perlin, Ken. "An Image Synthesizer." *Computer Graphics* (SIGGRAPH) 19(3), 1985.
- Perlin, Ken. "Improving Noise." *ACM Transactions on Graphics* (SIGGRAPH) 21(3), 2002. https://mrl.cs.nyu.edu/~perlin/paper445.pdf
- Ebert, Musgrave, Peachey, Perlin & Worley. *Texturing & Modeling: A Procedural Approach* (3rd ed., 2003) — esp. Musgrave, "Procedural Fractal Terrains." https://www.classes.cs.uchicago.edu/archive/2015/fall/23700-1/final-project/MusgraveTerrain00.pdf
- Quilez, Inigo. "Domain warping." https://iquilezles.org/articles/warp/ · "fBm." https://iquilezles.org/articles/fbm/
- Lorensen, William E. & Harvey E. Cline. "Marching Cubes: A High Resolution 3D Surface Construction Algorithm." *Computer Graphics* (SIGGRAPH) 21(4), 1987.
- Ju, Tao, Frank Losasso, Scott Schaefer & Joe Warren. "Dual Contouring of Hermite Data." *ACM Transactions on Graphics* (SIGGRAPH) 2002, pp. 43–52. https://www.cs.rice.edu/~jwarren/papers/dmc.pdf
- Lindenmayer, Aristid. "Mathematical models for cellular interactions in development." *Journal of Theoretical Biology*, 1968 (origin of L-systems).
- Parish, Yoav I. H. & Pascal Müller. "Procedural Modeling of Cities." SIGGRAPH 2001 (shape grammars / later CityEngine CGA). https://www.researchgate.net/publication/220720591_Procedural_Modeling_of_Cities
- Merrell, Paul. "Model Synthesis." i3D 2007 / PhD thesis 2009. https://paulmerrell.org/model-synthesis/
- Gumin, Maxim. "WaveFunctionCollapse." 2016. https://github.com/mxgmn/WaveFunctionCollapse
- Bridson, Robert. "Fast Poisson Disk Sampling in Arbitrary Dimensions." SIGGRAPH sketches, 2007.

Talks, practitioner, and teaching sources:

- McKendrick, Innes. "Continuous World Generation in 'No Man's Sky'." GDC 2017. https://www.gdcvault.com/play/1024265/Continuous-World-Generation-in-No
- Stålberg, Oskar. "Wave Function Collapse in Bad North." EPC 2018. https://www.youtube.com/watch?v=0bcZb-SsnrA · Townscaper (WFC + marching cubes on an irregular half-edge grid).
- Merrell, Paul. "Beyond Wave Function Collapse: Procedural Modeling without Tiles." EPC 2024. https://www.youtube.com/watch?v=1tgMl92DAqk
- Lague, Sebastian. "Coding Adventure: Hydraulic Erosion." https://www.youtube.com/watch?v=eaXk97ujbPQ · code: https://github.com/SebLague/Hydraulic-Erosion
- Gildea, Nick. "Dual Contouring: Seams & LOD for Chunked Terrain." http://ngildea.blogspot.com/2014/09/dual-contouring-chunked-terrain.html
- Mikola Lysenko. "Smooth Voxel Terrain (Part 2)." 0 FPS. https://0fps.net/2012/07/12/smooth-voxel-terrain-part-2/
- "The Book of Shaders: Fractal Brownian Motion." https://thebookofshaders.com/13/
- Barré-Brisebois et al. / vterrain.org — terrain LOD papers (chunked LOD, geometry clipmaps, skirts). http://vterrain.org/LOD/Papers/
- Bridson's algorithm (improved) — Extreme Learning. https://extremelearning.com.au/an-improved-version-of-bridsons-algorithm-n-for-poisson-disc-sampling/
- Stefan Gustavson, "Simplex noise demystified"; "Efficient computational noise in GLSL." https://arxiv.org/pdf/1204.1461
- "Whittaker Diagram." Procedural Content Generation Wiki. http://pcg.wikidot.com/pcg-algorithm:whittaker-diagram
- Voxel Tools (Godot) performance docs. https://voxel-tools.readthedocs.io/en/latest/performance/
- GPU Gems 2, Ch. 26: "Implementing Improved Perlin Noise." https://developer.nvidia.com/gpugems/gpugems2/part-iii-high-quality-rendering/chapter-26-implementing-improved-perlin-noise
- "Voxel cave generation using 3D Perlin noise isosurfaces." https://blog.danol.cz/voxel-cave-generation-using-3d-perlin-noise-isosurfaces/ ⚠️
- "terrain-erosion-3-ways" (hydraulic/fluvial/thermal comparison). https://github.com/dandrino/terrain-erosion-3-ways
