# T2 — Shaders & VFX: Technical-Craft Research

> **Research document** — seed for the `shaders-and-vfx` technical-craft skill.
> Written by the gamestack research agent; adversarially verified against independent sources.
> Engine-agnostic, design/spec register: principles, data, cost models, and "Test for:" criteria —
> **no engine-specific shader code** (no GLSL/HLSL/GDScript). Verification tags:
> ✅ ≥2 independent/primary sources · ⚠️ single source · ❌ refuted or substantially contested.

---

## 1. TL;DR

- **The shader model is a data-flow contract, not a coding style.** A vertex shader transforms per-vertex data and *writes* interpolated outputs; a fragment shader reads those interpolations and *writes one pixel*; a compute shader reads/writes arbitrary buffers (SSBOs) outside the raster pipeline. What a stage can write is the design constraint a generator must respect — you author material identity in the parts that are *safe to vary* (uniforms, textures, parameters), not in the pipeline plumbing. ✅
- **Procedural material authoring is parametric by construction, which is exactly what a generator wants.** Node-graph thinking (composable, typed operations), noise-based texturing, triplanar projection, and vertex-color masking all expose a *bounded parameter surface* a generator can vary safely — versus baked textures, which are unique per-asset and don't parameterize. The tradeoff is runtime cost vs. memory and the human art pass baked maps still win on. ✅
- **VFX is the rendering substrate for game-feel's feedback channels — and the single biggest fill-rate risk.** Particles, trails, decals, distortion, and screen-space effects (dissolve, hit-flash, rim) are *how* a hit-flash or impact burst is built; `game-feel-and-juice` owns *when and how much*. GPU particles scale to hundreds of thousands but carry a high fixed base cost; CPU particles are cheap at low counts and fall off a cliff. ✅
- **Stylized/NPR rendering is frequently cheaper AND more readable than PBR, not merely an aesthetic.** Cel shading (banded lighting via hand-controlled normals), outline rendering (inverted-hull vs. post-process edge detection — different cost/quality tradeoffs), and flat-shaded looks reduce shader and texture cost while improving gameplay-distance legibility, and they age better because they don't compete with advancing realism. ✅
- **The named failure modes are shader-variant explosion (PSO compilation stutter), overdraw from layered transparency, and the "particle storm."** Each is measurable: variant/PSO count, overdraw pixels-per-frame, and on-screen transparent-layer depth. A generator needs a fill-rate/overdraw budget validator and a variant-count cap, mirroring the feedback-budget pattern from `game-feel-and-juice`. ✅

---

## 2. Key Findings

**1. The vertex/fragment/compute split exists because each stage has a different read/write capability, not just a different job.** ✅
Vertex and fragment shaders can *read* arbitrary data (textures, uniforms) but can only *write* to fixed destinations — a vertex shader writes a transformed vertex plus interpolated outputs ("varyings"); a fragment shader writes exactly one pixel's color/depth. Compute shaders break this constraint: they write to Shader Storage Buffer Objects (SSBOs) and exchange data between stages, enabling GPU-driven particles and simulation (Arm Mali GPU developer guide; kindatechnical "Shaders: Vertex, Fragment, and Compute"). The Book of Shaders (Patricio Gonzalez Vivo) documents the three data channels into a fragment shader: **uniforms** (read-only, identical for every thread — `u_time`, `u_resolution`), **varyings** (per-vertex values interpolated across the primitive by the rasterizer), and **textures** (random-access sampled data). This is the authoring surface: a generator varies uniforms/textures, never the pipeline.

**2. "Calculate in the vertex shader, use in the fragment shader" is the foundational cost rule.** ✅
Because a scene has far more pixels (fragment invocations) than vertices, work moved up to the vertex stage runs orders of magnitude fewer times. Arm's and Kanzi's shader best-practice guides both state this as the first optimization. The corollary failure mode is per-pixel work that didn't need to be per-pixel (e.g., normalizing a constant in the fragment shader).

**3. Procedural texturing trades memory for ALU and yields a parametric authoring surface.** ✅
Noise functions (Perlin/Simplex/value), signed distance fields, triplanar projection, and vertex-color masking generate surface detail from math instead of sampling stored bitmaps. Inigo Quilez's body of work (iquilezles.org) is the canonical reference for procedural SDF primitives/operators and noise-based displacement. Triplanar mapping (Catlike Coding; Ryan DowlingSoka) needs *no UVs or tangents* — it projects from the three world axes and blends by surface normal — making it the default for runtime-generated geometry (terrain, caves) that has no UV unwrap. The cost is three texture samples instead of one.

**4. Combining fBm noise with SDFs requires redefining "addition," because the arithmetic sum of two SDFs is not an SDF.** ✅
Quilez ("fBM-SDF," iquilezles.org/articles/fbmsdf) identifies that ordinary additive fBm displacement breaks the SDF invariant (gradient length = 1.0), making raymarchers fail. His fix replaces addition with **smooth-clamp (smax)** to confine each noise octave to the surface vicinity and **smooth-union (smin)** to merge octaves with the host surface — producing a valid SDF suitable for distance-based lighting and raymarching. The named artifact is "flyovers" (disconnected surface fragments) at steep detail. This is the clearest worked example of why procedural techniques carry mathematical contracts a generator must not violate.

**5. GPU vs. CPU particles is a base-cost-vs-scaling tradeoff with a measurable crossover.** ✅
A Godot 2D study (Godot forum, "CPU vs GPU Particles 2D Performance Study") and Unreal's own CPU/GPU sprite comparison agree on the shape: CPU particles are cheap at tiny counts and degrade steeply (one test: 60→35 FPS at 5 particles, 7 FPS at 500); GPU particles carry a high *fixed* base cost but scale almost flat (13 FPS at both 5 and 500 in the same test). GPU particles also *lose features* — light emission, per-particle material parameters, and direct gameplay/Blueprint communication are CPU-only in Unreal. Rule: GPU particles for high counts/visual-only effects; CPU particles for low counts or gameplay-coupled effects.

**6. Cel shading is achieved by controlling lighting thresholds and surface normals, and the result is "all thanks to great artistic work," not a special algorithm.** ✅
Junya C. Motomura's GDC talk on *Guilty Gear Xrd* ("GuiltyGearXrd's Art Style: The X Factor Between 2D and 3D," GDC Vault) details: banded (thresholded) lighting, **hand-edited vertex normals** to control exactly where the light/shadow boundary falls, and **per-character lights** (no global lighting) animated per shot. The team's stated takeaway is that the tech is unremarkable; the labor is artistic (community GDC summaries, NeoGAF; galloscript/GGXrdShading demo repo reproduces the technique). *Hi-Fi Rush* (Kosuke Tanaka & Takashi Komada, GDC 2024) built a **deferred toon renderer** in customized UE4 — comic shader, toon lighting, static shadow maps, special face-shadow rendering — running 60 FPS at native resolution, around three keywords: "Colorful, Sharp, Clean," with no color gradations.

**7. Outline rendering is a two-family choice with opposite failure modes.** ✅
**Inverted hull** (extrude a back-facing duplicate along normals, render it solid) gives per-character control of width/color and scales line width with distance, but doubles polygon count, clips on hard edges/concave areas, and leaves holes on non-convex meshes. **Post-process edge detection** (depth/normal discontinuity in screen space) never clips into geometry and catches more edges, but is prone to noise and usually needs multiple detectors (Unreal Fest Japan "Stylized Rendering Insights from Japan" 2023; Ronja's post-processing-outlines; Kodeco UE4 toon outlines). There is no universally cheaper option — it depends on mesh topology and edge requirements.

**8. Shader-variant / PSO explosion is the dominant modern stutter source, and it is a combinatorial-count problem.** ✅
Each unique combination of keywords, vertex format, blend mode, and render-target config is a distinct shader *variant* (Unity) / Pipeline State Object (Unreal). Variant spaces routinely reach millions before filtering; large open worlds easily exceed 10,000 unique PSOs (Unity "Shader Variants Optimization"; Unreal "Game engines and shader stuttering"; MJP "The Shader Permutation Problem"). The first time the renderer meets a new combination it compiles a PSO *on the frame's critical path* — a couple to tens of milliseconds — producing a visible hitch. The fix is precaching (predict and compile PSOs at load/spawn) plus cutting rarely-used permutations.

**9. Overdraw from layered transparency is the primary fill-rate failure, and mobile is fill-rate-bound by default.** ✅
Transparent elements — particles, UI, sprites — stack and force the GPU to shade the same pixel many times; particle systems are singled out as "excel[ling] at creating overdraw" (Unity manual; TheGamedev.Guru; Android "Reduce overdraw"; GPU Gems 3 Ch.23 "High-Speed, Off-Screen Particles"). Mobile fill-rate ≈ screen pixels × shader complexity × overdraw, so even simple scenes become fill-rate-bound. The mitigations: simplest possible particle shaders, fewer/larger particles over many tiny ones, off-screen (half-res) particle buffers, and capping transparent-layer depth.

**10. Stylized rendering is widely held to be cheaper to produce, cheaper to run, more readable, and more durable — but the strongest of these claims is industry consensus, not controlled measurement.** ⚠️
Multiple studio sources argue NPR/stylized is less demanding than PBR, easier to keep readable at gameplay distance, and ages better because it doesn't chase advancing realism (N-iX, RocketBrush, VFX Apprentice, gamineai). The *direction* is corroborated by the shipped exemplars (Xrd, Hi-Fi Rush, TF2, Wind Waker) hitting high frame rates with strong legibility. Marked ⚠️ because "ages better" and "cheaper" are aesthetic/production judgments and community/industry opinion, not an empirical study like Kao's juiciness work.

---

## 3. Details by Sub-domain

---

### Sub-domain A — The Shader Model (the data-flow contract)

**Foundation:** *The Book of Shaders* (Patricio Gonzalez Vivo & Jen Lowe); Arm Mali GPU developer guide; GPU Gems.

#### A.1 Respect what each stage can write

**Rule:** Place each computation in the stage whose write-capability it needs — vertex (transform + emit interpolated outputs), fragment (one pixel out), compute (arbitrary buffer read/write). Don't try to do in a fragment shader what needs a compute shader's scatter writes.

- **Exemplar/source:** Arm Mali developer guide and kindatechnical's shader lesson both define the read/write asymmetry — vertex/fragment read arbitrary data but write fixed destinations; compute writes SSBOs and exchanges data between stages. ✅
- **Test for:** every per-pixel computation genuinely varies per pixel; any value constant across a primitive is computed in the vertex stage or passed as a uniform, not recomputed per fragment.
- **Failure mode — Per-pixel waste:** constant or per-vertex math run in the fragment shader, multiplying cost by the pixel-to-vertex ratio.

**→ Procedural / headless implication.** The generator's authoring surface is **uniforms, textures, and parameters** — the data channels — *never* the stage structure. A material "recipe" varies inputs into a fixed, hand-authored shader; it must not synthesize new shader stage code, because that re-opens variant explosion (A is the contract that bounds E.1).

> **Scoping note — the other programmable stages.** The pipeline also exposes optional geometry, tessellation, and (on modern hardware) mesh/amplification shaders, plus the fixed-function rasterizer and output-merge stages. They matter for specific effects (tessellated terrain, GPU-driven culling) but are deliberately out of this skill's load-bearing core: **vertex, fragment, and compute are the three a generator must reason about**, because they cover the overwhelming majority of material and VFX work and have the clearest, most portable read/write contracts. Geometry/tessellation shaders are also performance-fragile and poorly supported on some mobile/WebGL targets — treat them as a hand-authored, platform-gated optimization, not a generator surface.

#### A.2 Know the three data channels into a fragment shader

**Rule:** Feed per-pixel logic through the right channel — **uniforms** for values identical across the draw (time, color, parameters), **varyings** for values that interpolate across the surface (world position, normal, UV), **textures** for random-access spatial data. Pick the cheapest channel that carries the information.

- **Source:** Book of Shaders ch.3 (uniforms) and glossary (varying) — uniforms are read-only and uniform across threads; varyings are written in the vertex shader and interpolated by the rasterizer. ✅
- **Test for:** a material's varying inputs (per-vertex data) are minimized — only what genuinely needs interpolation crosses the vertex→fragment boundary, since each varying costs interpolator bandwidth.
- **Failure mode — Over-stuffed interpolators:** passing data through varyings that could be a uniform or recomputed cheaply, exhausting the limited interpolator slots and forcing variant splits.

---

### Sub-domain B — Material Authoring Patterns

**Foundation:** Inigo Quilez (iquilezles.org); Catlike Coding (triplanar); polycount wiki (multitexture/vertex blending); *Texturing & Modeling: A Procedural Approach* (Ebert et al.).

#### B.1 Think in a node graph: composable, typed, parametric operations

**Rule:** Author materials as a graph of small composable operations (sample → mask → blend → output) so material *identity* is a set of parameters, not a monolith. This is what lets a generator vary a material safely within bounded ranges.

- **Exemplar:** Shader-graph/node-material systems across engines model materials as directed graphs of typed nodes; the same mental model maps onto procedural shader composition. Procedural SDF construction (Quilez) is itself graph-like — primitives combined by union/intersection/smooth operators. ✅
- **Test for:** every generator-facing material exposes a documented parameter set with min/max ranges; no parameter, at any value in range, produces a broken material (NaN, inverted, invisible).
- **Failure mode — Unbounded parameter surface:** a generator drives a parameter past its valid range (negative roughness, zero-length normal) and ships a broken-looking or non-manifold material.

#### B.2 Use procedural texturing where it parameterizes; bake where it doesn't

**Rule:** Prefer procedural texturing (noise, SDF, triplanar, vertex-color masks) for surfaces that must *vary* across generated content or that *have no UVs* (runtime geometry); prefer baked textures for unique, art-directed, hand-detailed surfaces. The tradeoff is ALU + sampling cost (procedural) vs. memory + a per-asset human art pass (baked).

- **Exemplars:**
  - **Triplanar** mapping needs no UVs/tangents and never stretches or seams; it is "useful for procedural geometry of arbitrary shapes... terrain or cave systems at run-time" where generating UVs is infeasible (Catlike Coding; Cinema 4D triplanar tip). Cost: three samples + a normal-based blend per texture. ✅
  - **Vertex-color / splat-map masking** blends multiple materials per-pixel from a cheap control channel; vertex color is "fairly cheap" but a splat-map bitmap gives more control and avoids vertex-gradient artifacts. Used directly in procedural terrain, where elevation/slope rules drive the mask (polycount; Godot splat shaders; Grokipedia "Texture splatting"). ✅
  - **Noise/SDF** generate detail and shape from math — Quilez's primitives/operators are the reference library; periodic/symmetric SDFs give free instancing at zero memory cost. ✅
- **Test for:** runtime-generated meshes use triplanar or another UV-free path (no reliance on a UV unwrap that doesn't exist); procedural material parameters are seeded deterministically so a given seed reproduces the same surface.
- **Failure mode — UV dependence on UV-less geometry:** applying a UV-based material to runtime marching-cubes/voxel geometry, producing stretched/garbage texturing. **Second failure — procedural sameness:** every surface uses the same noise basis at the same scale, so parameter variation alone reads as "all the same material" (the oatmeal problem — see → implication).

**The baked-vs-procedural tradeoff, stated explicitly:**

| Axis | Procedural (noise/SDF/triplanar/mask) | Baked (authored bitmap) |
|---|---|---|
| **Runtime cost** | Higher — ALU + multiple samples per pixel (triplanar = 3×) | Lower — one sample per texture |
| **Memory** | Near-zero — generated from math | Higher — stored maps, often multiple per material |
| **Parameterizes for a generator?** | Yes — bounded parameter surface, varies safely | No — unique per-asset, doesn't interpolate |
| **Needs UVs?** | Triplanar/world-space variants need none | Yes — requires a UV unwrap |
| **Human art pass?** | Optional — math gives the base look | Required — an artist authors each map |
| **Best for** | Generated/runtime geometry, families of varied surfaces, terrain | Hero assets, art-directed detail, exact control |

A hybrid is normal and recommended: procedural base + masks (cheap, varied, generator-driven) with **baked detail/normal maps on hero assets** for the surfaces a player looks at closely. The decision is per-surface, not project-wide.

#### B.3 Keep fBm-on-SDF valid: redefine addition

**Rule:** When adding noise detail to an SDF, do **not** arithmetically add the noise — clamp each octave to the surface vicinity (smooth-max) and merge with smooth-min, preserving the unit-gradient SDF invariant.

- **Source:** Quilez, "fBM-SDF" — arithmetic addition of two SDFs is not an SDF and breaks raymarching; smax-confine + smin-merge produces a valid result. Named artifact: "flyovers" (disconnected fragments) at steep detail. ✅
- **Test for:** any procedural SDF displacement preserves approximate unit gradient (a raymarcher converges without overshoot artifacts); inspect for disconnected "flyover" fragments at high octave counts.
- **Failure mode — Broken SDF field:** additive displacement yields a non-distance field; raymarchers miss surfaces or render holes.

**→ Procedural / headless implication.** Procedural material is the *ideal* generator surface — but parameter variation alone does not guarantee perceptual variety (the **oatmeal test**, `procgen-review`). Bias toward **multiplicative** parameter spaces (palette × noise-basis × scale × mask) over a single slider, and keep hand-authored anchors (a few art-directed hero materials) so generated fill reads as a family, not as noise. Baked, hand-detailed textures remain a required human pass for hero assets.

---

### Sub-domain C — The VFX Toolkit (the rendering substrate for feedback channels)

> **Boundary with `game-feel-and-juice`:** that skill owns *when* a feedback channel fires, *why*, and its **budget** (the inverted-U ceiling); **this** skill owns *how each channel is technically built and what it costs.* Every primitive below maps to a juice-toolkit entry there.

**Foundation:** GPU Gems 3 (particles); Unity/Unreal particle docs; real-time VFX practitioner literature.

#### C.1 Particle systems — choose CPU vs. GPU by count and coupling

**Rule:** Use **GPU particles** for high counts and visual-only effects (smoke, sparks, debris fields); use **CPU particles** for low counts or effects that must talk to gameplay (collision events, spawning logic, lights). Decide by the count×coupling quadrant, not by default.

- **Source:** Godot 2D study + Unreal CPU/GPU comparison — GPU particles scale near-flat from high base cost; CPU particles are cheap at low counts and fall off a cliff; GPU particles lose light emission, per-particle material params, and gameplay/Blueprint communication. ✅ (specific FPS numbers ⚠️, single study)
- **Maps to:** `game-feel-and-juice` C.6 (particles) — this is its cost model.
- **Test for:** every particle system declares a max particle count and an emitter cap; high-count systems are GPU-simulated; gameplay-coupled systems are CPU and stay under their low-count budget.
- **Failure mode — Wrong-engine particles:** thousands of CPU particles tanking the frame, or a GPU system that can't trigger the gameplay event it was supposed to drive.

#### C.2 Trails & ribbons — connect particles into a strip

**Rule:** For continuous motion effects (sword slashes, projectile trails), build a **ribbon/trail** — a mesh strip connecting successive positions — rather than a stream of discrete quads. Ribbons always face the camera; trails follow geometry.

- **Source:** Stride/Unity particle docs — ribbons connect particles into a camera-facing strip (sword slashes); stretched-billboard mode aligns and elongates a particle along its velocity for motion-blur-style streaks (bullets, impacts). ✅
- **Maps to:** `game-feel-and-juice` C.2/C.3 (follow-through, motion weight).
- **Test for:** fast-moving impact/projectile effects use velocity-stretched billboards or ribbons (not single static sprites that strobe at speed); ribbon segment count is capped.
- **Failure mode — Strobing trail:** discrete sprites at high velocity read as a dotted line instead of a continuous arc.

#### C.3 Decals — project surface detail without editing the mesh

**Rule:** Use **deferred/screen-space decals** (project a box onto reconstructed G-buffer position) for bullet holes, scorch marks, blood, and footprints — they modify the G-buffer before lighting, so multiple decals cost one lighting pass per pixel.

- **Source:** multiple deferred-decal references (IceFall Games; Bart Wronski; martindevans) — screen-space decals reconstruct position from depth and write into the G-buffer with "no extra lighting cost"; the cost is extra screen-sized buffers in memory and forward-rendered decals being more expensive. ✅
- **Maps to:** `game-feel-and-juice` polish (persistent impact evidence).
- **Test for:** dynamic surface marks use projected decals, not unique mesh edits or material swaps; decal count on screen is bounded.
- **Failure mode — Decal projection bleed:** screen-space decals projecting onto unintended surfaces at grazing angles (the classic artifact Wronski documents) — clamp by surface-normal angle.

#### C.4 Distortion & refraction — sample the framebuffer

**Rule:** Build heat-haze, shockwaves, glass, and water refraction by **sampling the already-rendered scene** (grab-pass / scene-color) with a UV offset from a normal/flow map. Treat the grab as expensive and share one scene-color copy across all distortion in a frame.

- **Source:** Poiyomi grab-pass docs + practitioner tutorials — grab-pass "takes a screenshot of the scene every frame... always cause[s] some amount of performance hit"; distortion offsets the sampled UVs by a wave/normal map. ✅
- **Maps to:** `game-feel-and-juice` polish (impact shockwaves, magical fields).
- **Test for:** all distortion effects in a frame read from a single shared scene-color capture, not one grab per effect; distortion is disabled or simplified on platforms without cheap framebuffer fetch.
- **Failure mode — Grab-pass-per-effect:** each distortion sprite forcing its own full-screen copy, multiplying bandwidth.

#### C.5 Screen-space material effects — dissolve, hit-flash, rim/Fresnel

**Rule:** Build state-feedback effects in the material itself: **dissolve** (threshold a noise texture against a progress uniform to erode a mesh), **hit-flash** (lerp toward a flat emissive color on a damage uniform), **rim/Fresnel** (intensity from the angle between normal and view direction) for silhouette emphasis and selection highlights.

- **Source:** Unity Shader Graph Fresnel node + lettier "Rim Lighting" — Fresnel reflectance varies with view angle; "often used to achieve rim lighting." Grab-pass/dissolve practitioner refs. ✅
- **Maps to:** `game-feel-and-juice` C.4-adjacent (hit-flash is the *visual* half of impact feedback; hit-stop is the *temporal* half).
- **Test for:** hit-flash, dissolve, and rim are driven by a single scalar uniform each (damage/progress/none), so a generator drives them by value; the flash color and rim color come from the art-direction signal palette (→ `art-direction-and-readability`), not invented per-effect.
- **Failure mode — Rim everywhere:** Fresnel rim applied to every object as "polish" until nothing reads as special — the inverted-U from `game-feel-and-juice` applied to a single channel.

**→ Procedural / headless implication.** Expose the VFX toolkit as a **parametric recipe palette**: each effect is a hand-authored template (emitter shape, particle shader, color from the signal palette, count range) with safe parameter bounds; the generator *selects and parameterizes*, never authors new emitters or shaders. Pair the palette with a per-scene fill-rate/overdraw budget (mirroring the feedback-budget table in `game-feel-and-juice`) so generated VFX cannot exceed the readability/performance ceiling.

---

### Sub-domain D — Stylization Techniques

**Foundation:** Motomura GDC (*Guilty Gear Xrd*); Tanaka & Komada GDC 2024 (*Hi-Fi Rush*); Unreal Fest Japan 2023 (stylized rendering); Ronja's tutorials.

#### D.1 Cel/toon shading — band the lighting, control the normals

**Rule:** Achieve a cel look by **thresholding** the lighting response into flat bands and **controlling surface normals** (often hand-edited) so the light/shadow boundary lands exactly where the art wants it. Light per-character rather than globally where shot-by-shot control is needed.

- **Exemplar:** *Guilty Gear Xrd* (Motomura, GDC Vault) — thresholded lighting, hand-edited vertex normals to place the shadow line, per-character animated lights, no global lighting; the explicit lesson is "nothing special tech-wise — the result is all thanks to great artistic work." ✅ Reproduced by galloscript/GGXrdShading.
- **Test for:** the lighting ramp is a small set of bands (not a smooth gradient); normals are an authorable input the generator can be handed, not assumed to be the geometric normal.
- **Failure mode — Muddy banding:** a toon ramp applied to geometric normals on noisy procedural meshes produces broken, flickering shadow lines (this is *why* hand-normals matter and why procedural toon characters need a normal-smoothing pass).

#### D.2 Outline rendering — pick the family by topology

**Rule:** Choose **inverted-hull** outlines when you need per-object width/color control on smooth convex characters and can afford the doubled polys; choose **post-process edge detection** when you need geometry-clip-free outlines and consistent screen-space width across the whole scene.

- **Source:** Unreal Fest Japan 2023 + Ronja + Kodeco — inverted hull: per-character control, width scales with distance, but doubles polys, clips on hard edges, holes on concave meshes; post-process: no geometry clipping, catches more edges, but noisy and needs multiple detectors. ✅
- **Test for:** the chosen method matches mesh topology — inverted hull only on meshes verified smooth/convex enough not to hole; post-process tuned with depth+normal detectors to suppress interior noise.
- **Failure mode — Inverted-hull holes:** hard-edged or concave generated meshes show gaps/spikes in the outline; **post-process noise:** high-frequency surfaces light up with spurious interior edges.

#### D.3 Treat stylization as a performance and readability decision

**Rule:** Reach for stylized/NPR when the budget is tight or readability at gameplay distance matters — flat shading, fewer texture samples, and banded lighting cost less than full PBR and make gameplay-critical elements (enemies, hazards, interactables) easier to distinguish.

- **Source:** industry/studio consensus (N-iX, RocketBrush, VFX Apprentice, gamineai) + the shipped exemplars (Xrd, Hi-Fi Rush at 60 FPS native). "Readability at real gameplay distance should be judged first"; stylized "doesn't compete with ever-improving graphics technology" so it ages better. ⚠️ (direction corroborated by exemplars; "ages better"/"cheaper" are judgments, not measured)
- **Maps to:** `art-direction-and-readability` (owns the *art-direction* and signal-language decision; this skill owns the *rendering technique* that realizes it).
- **Test for:** at gameplay camera distance, enemies/hazards/interactables are distinguishable by silhouette and signal color within a fixed glance time; the style hits its frame-rate target on min-spec.
- **Failure mode — Fidelity over legibility:** a render that looks great in a close-up beauty shot but loses gameplay-critical elements in clutter at play distance.

**→ Procedural / headless implication.** Stylized pipelines are *more* generator-friendly: flat/banded materials have fewer parameters to get wrong, and a fixed signal palette (from `art-direction-and-readability`) keeps generated content readable by construction. The required human pass is normal authoring/smoothing for toon characters and topology-checking meshes before applying inverted-hull outlines.

---

### Sub-domain E — Performance & Failure Modes

**Foundation:** Unreal "Game engines and shader stuttering"; MJP "The Shader Permutation Problem"; Unity shader-variant docs; GPU Gems 3 Ch.23; Android/Unity overdraw guides.

#### E.1 Shader-variant / PSO explosion — cap the combinatorics, precache the rest

**Rule:** Treat every keyword/feature toggle as a multiplier on variant count and keep the multiplier low; precompile (precache) the PSOs you will actually use at load/spawn, and cut rarely-used permutations.

- **Source:** Unity + Unreal + MJP — variant spaces reach millions; open worlds exceed 10,000 PSOs; first-encounter PSO compile happens on the frame critical path (a few–tens of ms) and stutters; precaching + permutation-cutting is the fix. ✅
- **Test for:** total shipped variant/PSO count is measured and bounded; a precache pass (playthrough-collected or predicted) compiles hot PSOs before gameplay; no new PSO compiles during steady-state play (profile for compile-on-critical-path hitches).
- **Failure mode — Compilation stutter:** traversal/combat hitches the first time each new material/state combination appears. **Root cause — keyword sprawl:** an über-shader with dozens of toggles whose product is unshippable.

#### E.2 Overdraw — bound transparent-layer depth and fill rate

**Rule:** Budget overdraw explicitly — minimize stacked transparent layers, use the simplest shader on particles, prefer fewer large particles over many tiny ones, and render dense particle systems to an off-screen (half-res) buffer.

- **Source:** Unity manual + TheGamedev.Guru + Android + GPU Gems 3 Ch.23 — particles "excel at creating overdraw"; mobile fill-rate ≈ pixels × shader complexity × overdraw; off-screen half-res particles are the canonical mitigation. ✅
- **Maps to:** `game-feel-and-juice` C.6 failure mode ("performance-busting particle storms") — this is the rendering-cost mechanism behind it.
- **Test for:** an overdraw debug view stays under a per-scene ceiling at peak effect load; particle shaders are confirmed cheap (no per-pixel lighting on smoke); transparent UI and VFX layer depth is capped.
- **Failure mode — Fill-rate collapse:** layered transparent VFX shading each pixel 10×+, dropping frame rate on exactly the climactic moments that spawn the most effects.

#### E.3 The particle storm — the anti-pattern that tanks FPS *and* readability

**Rule:** Cap simultaneous on-screen VFX so effects communicate instead of obscure. The particle storm fails twice — it breaks frame rate (E.2) *and* hides the gameplay state the VFX was meant to convey.

- **Source:** practitioner + community consensus — *Tekken 7* particle effects "overshadow and obscure character movements"; *Ori and the Will of the Wisps* "literally unreadable with all the light effects"; *Overwatch* held up as the counter-example where "every effect remains readable, even when dozens... appear at once." "Exercise restraint — if all effects are flashy, none stand out." ⚠️ (community/practitioner opinion, well-attested across sources)
- **Maps to:** `game-feel-and-juice` C.9 (the inverted-U) — the particle storm is the inverted-U violated in the VFX channel specifically.
- **Test for:** at maximum simultaneous combat/ability load, gameplay-critical signals (enemy tells, hazard zones, player position) remain identifiable; total concurrent emitters and screen-coverage stay under budget.
- **Failure mode — Spectacle over communication:** measuring VFX by quantity, so the screen saturates and the player can't read the attack they have a split second to respond to.

**→ Procedural / headless implication.** All three failures are machine-checkable and belong in a **VFX/shader budget validator** run on every generated batch: (1) variant/PSO count under cap with a precache manifest; (2) overdraw under the per-scene ceiling at simulated peak load; (3) concurrent-emitter and screen-coverage caps enforced. This mirrors the juice-budget linter from `game-feel-and-juice` and routes failures back to the generator like `procgen-review`.

---

## 4. Recommendations

**Stage 1 — Fix the pipeline contract (before any generation):**
1. Define the **shader-stage contract**: a small set of hand-authored, parameterized master materials (opaque, transparent, toon, etc.). The generator varies their *parameters and textures only* — never authors shader code. This caps variant explosion at the source (A, E.1).
2. Lock the **exposure/tonemapping/color pipeline** (owned with `3d-graphics-and-rendering`) so all generated content shares one color contract — procedural materials read as incoherent otherwise.
3. Author the **signal/effect palette** with `art-direction-and-readability`: the fixed set of hit-flash colors, rim colors, dissolve patterns, and particle colors generation may draw from.

**Stage 2 — Build the parametric material/VFX palette:**
4. Author master materials as **node graphs** with documented, range-bounded parameters (B.1); validate that no in-range value breaks the material.
5. Provide **UV-free texturing** (triplanar) and **mask-based blending** (vertex-color/splat) paths for runtime-generated geometry (B.2).
6. Author the **VFX recipe palette**: each effect (hit-flash, impact burst, trail, decal, dissolve, distortion) is a hand-authored template with CPU/GPU choice fixed and count/size ranges bounded (C.1–C.5). Map each recipe to its `game-feel-and-juice` feedback channel.

**Stage 3 — Stylization pass:**
7. If stylized: implement the **toon ramp + normal-authoring** path (D.1) and pick the **outline family** per mesh topology (D.2); add a normal-smoothing/topology-check pre-pass for generated meshes.
8. Validate **readability at gameplay distance** (D.3): enemies/hazards/interactables distinguishable by silhouette + signal color within a glance.

**Stage 4 — Procedural-readiness validation (block commit on failure):**
9. **Variant/PSO validator** — total count under cap; precache manifest present; no steady-state compile hitches (E.1).
10. **Overdraw/fill-rate validator** — overdraw under per-scene ceiling at simulated peak VFX load (E.2).
11. **Concurrent-VFX validator** — emitter count and screen-coverage caps; gameplay signals still readable under max load (E.3).
12. **Material-parameter validator** — every generated material's parameters in range; runtime geometry uses a UV-free path; SDF displacement preserves the field (B.3).

**Thresholds that change the plan:**
- **Mobile / low-end target:** halve overdraw and particle budgets; prefer CPU particles at low counts (GPU base cost dominates); avoid grab-pass distortion (no cheap framebuffer fetch); off-screen half-res particles become mandatory, not optional.
- **Competitive / readability-critical (fighting, MOBA, shooter):** tighten the concurrent-VFX cap hard; reserve the brightest signal colors for gameplay-critical events only; treat the particle storm as a shipping blocker.
- **Raymarched / SDF-heavy (demoscene-style, stylized):** the fBm-SDF field-validity contract (B.3) becomes load-bearing; budget by raymarch step count, not draw calls.
- **Photoreal / PBR target:** stylization recommendations invert — but the variant, overdraw, and particle-storm validators (E) still apply unchanged.

---

## 5. Caveats

- **"Stylized is cheaper and ages better" is consensus, not measurement.** Unlike the Kao juiciness study, no controlled study establishes stylized < PBR on cost or longevity. The shipped exemplars (Xrd, Hi-Fi Rush, TF2, Wind Waker) strongly corroborate the *direction* — high frame rate, durable readability — but production cost depends on the specific style (hand-painted PBR can be *more* labor-intensive than realistic PBR). Treat as a strong default, not a law.
- **The CPU/GPU particle FPS numbers are one study.** The *shape* (CPU cheap-then-cliff, GPU high-base-then-flat) is corroborated by Unreal's own docs, but the exact crossover point is engine-, platform-, and effect-specific. Profile on target hardware; don't port the numbers.
- **Outline and cel techniques are mesh-topology-dependent.** Inverted-hull outlines and toon ramps assume well-behaved normals and topology. Procedurally generated meshes (marching cubes, voxel) frequently violate these assumptions — this is the single biggest place generated stylized content needs a hand-authored/automated cleanup pass (normal smoothing, topology check), and the source literature mostly assumes hand-authored meshes.
- **No raw shader code by design.** This document gives data-flow, cost models, and test criteria only. The actual GLSL/HLSL/GDScript/Shader-Graph implementation belongs in the engine-hand packs (`godot`/`unreal`/`unity`/`threejs`), per the design-here/implement-there split in `docs/architecture.md`.
- **AI-authoring adds a risk the source literature doesn't cover.** The shader/VFX sources assume a human technical artist who *sees* the overdraw, the variant count, the particle storm. A headless generator does not. The budget validators (E, Stage 4) are the substitute for that human eye and must be built before generation is trusted — and even then, perceptual sameness of procedural materials (the oatmeal problem) needs early playtest spot-checks, not just automated bounds-checking.

---

## Relationship to sibling skills

- **`game-feel-and-juice` (universal craft):** owns *when* a feedback channel fires, *why*, and its **budget** (the inverted-U ceiling). **This skill is its rendering substrate** — the technical build and cost model of every channel in its juice toolkit (particles, trails, screen distortion, hit-flash). The feedback-budget pattern there becomes the fill-rate/overdraw budget validator here. Cite *down* to this skill for "how is this effect built and what does it cost"; cite *up* for "should this effect fire and how big."
- **`3d-graphics-and-rendering` (technical craft, sibling):** owns the rendering *pipeline*, lighting, GI, shadows, post-processing order, and the overall performance-budget framing. This skill assumes its pipeline (forward/deferred, the color/tonemapping contract) and lives inside it at the material/shader/particle layer.
- **`art-direction-and-readability` (genre/universal):** owns the *art-direction* decision and the signal-color/silhouette language. This skill realizes that language as rendering technique (which palette entry the hit-flash uses, how the toon ramp is banded) but does not decide the language.
- **`procedural-geometry` (technical craft, sibling):** generates the meshes/terrain this skill textures and outlines; triplanar/vertex-color paths here exist because that skill's runtime geometry has no UVs.
- **`procgen-review` (process):** the budget validators here (variant count, overdraw, concurrent VFX, material parameters) are gates it runs on generated batches, routing failures back to the generator.

---

## Sources

Primary / technical:

- Gonzalez Vivo, Patricio, and Jen Lowe. *The Book of Shaders*. https://thebookofshaders.com/ (ch.3 uniforms; glossary: varying)
- Quilez, Inigo. "Signed Distance Functions," "fBM-SDF," and procedural-texturing articles. https://iquilezles.org/articles/distfunctions/ · https://iquilezles.org/articles/fbmsdf/
- Arm. "About compute shaders," Mali GPU OpenGL ES 3.x Developer Guide. https://developer.arm.com/documentation/100587/0101/Compute-shaders/About-compute-shaders
- kindatechnical. "Shaders: Vertex, Fragment, and Compute." https://kindatechnical.com/computer-graphics/lesson-52-shaders-vertex-fragment-and-compute.html
- NVIDIA. *GPU Gems 3*, Ch. 23 "High-Speed, Off-Screen Particles." https://developer.nvidia.com/gpugems/gpugems3/part-iv-image-effects/chapter-23-high-speed-screen-particles

Stylized-rendering / GDC:

- Motomura, Junya C. "GuiltyGearXrd's Art Style: The X Factor Between 2D and 3D." GDC. https://www.gdcvault.com/play/1022031/GuiltyGearXrd-s-Art-Style-The · GDC 2015 PDF: https://www.ggxrd.com/Motomura_Junya_GuiltyGearXrd.pdf · reproduction: https://github.com/galloscript/GGXrdShading
- Tanaka, Kosuke, and Takashi Komada. "3D Toon Rendering in 'Hi-Fi RUSH'." GDC 2024. https://gdcvault.com/play/1034330/3D-Toon-Rendering-in-Hi · making-of: https://80.lv/articles/the-making-of-hi-fi-rush-s-3d-toon-rendering-style
- Epic Games Japan. "Stylized Rendering Insights from Japan," Unreal Fest Gold Coast 2023. https://www.docswell.com/s/EpicGamesJapan/5DEVPV-2023-12-01-082936
- Ronja. "Outlines via Postprocessing." https://www.ronja-tutorials.com/post/019-postprocessing-outlines/

Performance / failure modes:

- Epic Games. "Game engines and shader stuttering: Unreal Engine's solution." https://www.unrealengine.com/tech-blog/game-engines-and-shader-stuttering-unreal-engines-solution-to-the-problem
- MJP (Matt Pettineo). "The Shader Permutation Problem — Part 1." https://therealmjp.github.io/posts/shader-permutations-part1/
- Unity. "Shader Variants Optimization & Troubleshooting Tips." https://unity.com/blog/engine-platform/shader-variants-optimization-troubleshooting-tips
- TheGamedev.Guru. "Unity Overdraw: Improving the GPU Performance of Your Game." https://thegamedev.guru/unity-gpu-performance/overdraw-optimization/
- Android Developers. "Reduce overdraw." https://developer.android.com/topic/performance/rendering/overdraw

Particles / VFX:

- Godot Forum. "CPU vs GPU Particles 2D Performance Study." https://forum.godotengine.org/t/cpu-vs-gpu-particles-2d-performance-study-careful-gpu-particles-seem-to-have-a-high-base-cost/67850
- Epic Games. "CPU and GPU Sprite Particles Comparison," UE4.27 docs. https://dev.epicgames.com/documentation/en-us/unreal-engine/1.1---cpu-and-gpu-sprite-particles-comparison
- Stride. "Ribbons and trails." https://doc.stride3d.net/4.0/en/manual/particles/ribbons-and-trails.html
- Wronski, Bart. "Fixing screen-space deferred decals." https://bartwronski.com/2015/03/12/fixing-screen-space-deferred-decals/
- Poiyomi. "Grab Pass." https://www.poiyomi.com/extended-features/grabpass

Material authoring:

- Catlike Coding. "Triplanar Mapping." https://catlikecoding.com/unity/tutorials/advanced-rendering/triplanar-mapping/
- DowlingSoka, Ryan. "Triplanar, Dithered Triplanar, and Biplanar Mapping in Unreal." https://ryandowlingsoka.com/unreal/triplanar-dither-biplanar/
- polycount wiki. "MultiTexture." http://wiki.polycount.com/wiki/MultiTexture
- Unity. "Fresnel Effect Node," Shader Graph docs. https://docs.unity3d.com/Packages/com.unity.shadergraph@6.9/manual/Fresnel-Effect-Node.html
- lettier. "Rim Lighting," 3D Game Shaders For Beginners. https://lettier.github.io/3d-game-shaders-for-beginners/rim-lighting.html

Stylized vs. PBR (industry opinion):

- N-iX Games. "Realistic vs Stylized Art in Video Game Development." https://gamestudio.n-ix.com/realistic-vs-stylized-art-in-video-game-development/
- VFX Apprentice. "How Style Guides Define The Look Of Your Game and VFX." https://www.vfxapprentice.com/blog/creating-vfx-style-guides-for-games
- INLINGO. "What VFX in Games Are and Why We Need Them." https://inlingogames.com/blog/what-vfx-in-games-are-and-why-we-need-them/
</content>
</invoke>
