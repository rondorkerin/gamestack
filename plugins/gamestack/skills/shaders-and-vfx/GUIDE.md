# Shaders & VFX — Guide

The discipline of authoring shader-driven materials and visual effects that are parametric, readable, and cheap: a shader is a data-flow contract, a material is a bounded parameter surface, VFX is feedback built out of fill rate. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from *The Book of Shaders* (Gonzalez Vivo) and Arm/GPU-Gems for the shader model; Inigo Quilez (iquilezles.org) for procedural texturing and SDFs; the stylized-rendering GDC lineage (*Guilty Gear Xrd* — Motomura; *Hi-Fi Rush* — Tanaka & Komada 2024; Unreal Fest Japan 2023); and the performance literature (Unreal/Unity/MJP on shader variants & PSO stutter; GPU Gems 3 Ch.23 and the overdraw guides). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested or industry-opinion. Full citations in the research doc (`docs/research/round3-technical/T2`).

> **The central law:** *vary parameters, not pipeline plumbing — and budget VFX like fill rate.* A generator feeds inputs into a small set of hand-authored master materials and a bounded VFX recipe palette; it never authors shader code or uncapped emitters. That caps variant explosion at the source and keeps the screen readable.

> This skill is the **rendering substrate for `game-feel-and-juice`** — it owns *how* each feedback channel is built and what it costs; that skill owns *when/why/how much* it fires. It lives inside the pipeline owned by `3d-graphics-and-rendering` and realizes the signal language owned by `art-direction-and-readability`.

---

# Sub-domain A — The Shader Model (the data-flow contract)

> The vertex/fragment/compute split exists because each stage has a different **write** capability, not just a different job. Vertex shaders transform per-vertex data and emit interpolated outputs; fragment shaders read those interpolations and write **one pixel**; compute shaders read/write arbitrary buffers (SSBOs) outside the raster pipeline. ✅ (Arm Mali guide; Book of Shaders.)

## A.1 Place each computation in the stage whose write-capability it needs

Don't do in a fragment shader what needs a compute shader's scatter writes; don't recompute per-pixel what's constant per primitive. The cost rule: **calculate in the vertex shader, use in the fragment shader** — far fewer vertices than pixels. ✅ (Arm/Kanzi best-practice guides.)

- **Test for:** every per-pixel computation genuinely varies per pixel; constant or per-vertex math lives in the vertex stage or a uniform.
- **Failure mode — Per-pixel waste:** constant/per-vertex math run per fragment, multiplying cost by the pixel-to-vertex ratio.

## A.2 Feed per-pixel logic through the right data channel

Three channels reach a fragment shader: **uniforms** (read-only, identical for every thread — time, color, parameters), **varyings** (per-vertex values the rasterizer interpolates — world position, normal, UV), **textures** (random-access spatial data). Pick the cheapest channel that carries the information. ✅ (Book of Shaders ch.3 + glossary.)

- **Test for:** varying inputs are minimized — only data that genuinely needs interpolation crosses the vertex→fragment boundary (interpolator slots are limited).
- **Failure mode — Over-stuffed interpolators:** passing as varyings what could be a uniform, exhausting slots and forcing variant splits.

**→ Procedural / headless implication.** The generator's authoring surface is **uniforms, textures, and parameters** — never the stage structure. A material recipe varies inputs into a fixed, hand-authored shader; synthesizing new shader code re-opens variant explosion (A is the contract that bounds E.1).

---

# Sub-domain B — Material Authoring Patterns

> Procedural authoring is parametric by construction — exactly what a generator wants. Node-graph thinking, noise/SDF texturing, triplanar projection, and vertex-color masking each expose a **bounded parameter surface**. Baked textures are unique per-asset and don't parameterize — the tradeoff is runtime ALU/sampling cost vs. memory + a human art pass.

## B.1 Think in a node graph — composable, typed, parametric

Author materials as a graph of small composable operations (sample → mask → blend → output) so material *identity* is a parameter set, not a monolith. ✅ (Shader-graph systems across engines; Quilez's SDF construction is itself graph-like — primitives combined by union/intersection/smooth operators.)

- **Test for:** every generator-facing material exposes a documented parameter set with min/max ranges; no in-range value produces a broken material (NaN, inverted, invisible).
- **Failure mode — Unbounded parameter surface:** a generator drives a parameter out of range (negative roughness, zero-length normal) and ships a broken material.

## B.2 Use procedural texturing where it parameterizes; bake where it doesn't

Prefer procedural (noise, SDF, triplanar, vertex-color masks) for surfaces that must *vary* across generated content or that *have no UVs*; prefer baked for unique, art-directed, hand-detailed surfaces.

- **Triplanar** needs no UVs or tangents — it projects from the three world axes and blends by surface normal, never stretching or seaming; the default for runtime geometry (terrain, caves) that has no unwrap. Cost: three samples + blend per texture. ✅ (Catlike Coding; DowlingSoka.)
- **Vertex-color / splat-map masking** blends materials per-pixel from a cheap control channel; vertex color is cheap, a splat bitmap gives more control and avoids gradient artifacts. Drives procedural terrain from elevation/slope rules. ✅ (polycount; Godot splat shaders; Grokipedia.)
- **Noise / SDF** generate detail and shape from math; periodic/symmetric SDFs give free instancing at zero memory cost. ✅ (Quilez.)
- **Test for:** runtime-generated meshes use a UV-free path (no reliance on an unwrap that doesn't exist); procedural parameters are seeded deterministically (a seed reproduces the surface).
- **Failure modes — UV dependence on UV-less geometry** (stretched/garbage texturing on voxel/marching-cubes meshes) · **procedural sameness** (one noise basis at one scale everywhere → every surface reads the same — the oatmeal problem).

## B.3 Keep fBm-on-SDF valid — redefine "addition"

When adding noise detail to an SDF, do **not** arithmetically add it (the sum of two SDFs is not an SDF — it breaks the unit-gradient invariant and raymarchers fail). Clamp each octave to the surface vicinity (smooth-max) and merge with smooth-min. ✅ (Quilez, "fBM-SDF"; named artifact: "flyovers" — disconnected fragments — at steep detail.)

- **Test for:** procedural SDF displacement preserves approximate unit gradient (a raymarcher converges without overshoot); no disconnected flyover fragments at high octave counts.
- **Failure mode — Broken SDF field:** additive displacement yields a non-distance field; raymarchers miss surfaces or render holes.

**→ Procedural / headless implication.** Procedural material is the *ideal* generator surface — but parameter variation alone doesn't guarantee perceptual variety (the **oatmeal test**, `procgen-review`). Bias toward **multiplicative** spaces (palette × noise-basis × scale × mask) over a single slider, and keep hand-authored hero materials as anchors so generated fill reads as a family. Baked, hand-detailed textures remain a required human pass for hero assets.

---

# Sub-domain C — The VFX Toolkit (the rendering substrate for feedback channels)

> Each primitive below is *how* a `game-feel-and-juice` feedback channel is built; that skill owns *when/why/how much*. Layered transparency is the #1 fill-rate cost — every primitive carries a cost model, not just a look.

## C.1 Particles — choose CPU vs. GPU by count and coupling

**GPU particles** for high counts and visual-only effects (smoke, sparks, debris); **CPU particles** for low counts or effects that must talk to gameplay (collision, spawn logic, lights). Decide by the count×coupling quadrant. ✅ (Godot study + Unreal docs: CPU is cheap at low counts then falls off a cliff; GPU has a high *fixed* base cost then scales near-flat, but loses light emission / per-particle params / gameplay communication. Specific FPS numbers ⚠️, single study.) → builds `game-feel-and-juice` C.6.

- **Test for:** every system declares a max count + emitter cap; high-count systems are GPU; gameplay-coupled systems are CPU and stay under their low-count budget.
- **Failure mode — Wrong-engine particles:** thousands of CPU particles tanking the frame, or a GPU system that can't trigger the gameplay event it was meant to drive.

## C.2 Trails & ribbons — connect particles into a strip

For continuous motion (sword slashes, projectile trails) build a **ribbon/trail** (a mesh strip connecting successive positions), not a stream of discrete quads; use **velocity-stretched billboards** for streaky impacts/bullets. Ribbons face the camera; trails follow geometry. ✅ (Stride/Unity docs.) → builds `game-feel-and-juice` C.2/C.3 (follow-through, motion weight).

- **Test for:** fast impact/projectile effects use stretched billboards or ribbons (not static sprites that strobe at speed); segment count capped.
- **Failure mode — Strobing trail:** discrete sprites at high velocity read as a dotted line, not a continuous arc.

## C.3 Decals — project surface detail without editing the mesh

Use **deferred/screen-space decals** (project a box onto reconstructed G-buffer position) for bullet holes, scorch, blood, footprints — they write the G-buffer *before* lighting, so multiple decals cost one lighting pass per pixel. ✅ (Wronski; IceFall; martindevans.) Cost: extra screen-sized buffers in memory. → persistent impact evidence (polish).

- **Test for:** dynamic surface marks use projected decals, not unique mesh edits; on-screen decal count bounded.
- **Failure mode — Decal projection bleed:** screen-space decals projecting onto unintended surfaces at grazing angles — clamp by surface-normal angle.

## C.4 Distortion & refraction — sample the framebuffer

Build heat-haze, shockwaves, glass, and water by **sampling the already-rendered scene** (grab-pass/scene-color) with a UV offset from a normal/flow map. The grab is expensive — share **one** scene-color copy across all distortion in a frame. ✅ (Poiyomi grab-pass: "takes a screenshot every frame... always a performance hit".) → impact shockwaves, magical fields (polish).

- **Test for:** all distortion in a frame reads from a single shared scene-color capture (not one grab per effect); distortion simplified/disabled where framebuffer fetch is costly (mobile).
- **Failure mode — Grab-pass-per-effect:** each distortion sprite forcing its own full-screen copy, multiplying bandwidth.

## C.5 Screen-space material effects — dissolve, hit-flash, rim/Fresnel

Build state-feedback in the material: **dissolve** (threshold a noise texture against a progress uniform), **hit-flash** (lerp toward a flat emissive on a damage uniform), **rim/Fresnel** (intensity from normal-vs-view angle) for silhouette emphasis and selection highlights. ✅ (Unity Fresnel node; lettier rim lighting.) Hit-flash is the *visual* half of impact; hit-stop (`game-feel-and-juice` C.4) is the *temporal* half.

- **Test for:** each effect is driven by a single scalar uniform (damage/progress), so a generator drives it by value; flash/rim colors come from the art-direction signal palette (→ `art-direction-and-readability`), not invented per-effect.
- **Failure mode — Rim everywhere:** Fresnel rim on every object as "polish" until nothing reads as special — the inverted-U applied to one channel.

**→ Procedural / headless implication.** Expose the toolkit as a **parametric recipe palette**: each effect is a hand-authored template (emitter shape, particle shader, color from the signal palette, count range) with safe bounds; the generator *selects and parameterizes*, never authors new emitters/shaders. Pair with a per-scene fill-rate/overdraw budget (mirroring the feedback-budget table in `game-feel-and-juice`) so generated VFX can't exceed the readability/performance ceiling.

---

# Sub-domain D — Stylization Techniques

> Stylized/NPR is frequently cheaper AND more readable than PBR — a performance and legibility decision, not only an aesthetic. It realizes the language owned by `art-direction-and-readability`.

## D.1 Cel/toon shading — band the lighting, control the normals

Threshold the lighting response into flat bands and **control the surface normals** (often hand-edited) so the light/shadow boundary lands where the art wants; light per-character where shot-by-shot control matters. ✅ (*Guilty Gear Xrd* — Motomura GDC: thresholded lighting, hand-edited vertex normals, per-character animated lights, no global lighting; the explicit lesson is "nothing special tech-wise — all thanks to great artistic work". Reproduced by galloscript/GGXrdShading.)

- **Test for:** the lighting ramp is a few discrete bands (not a smooth gradient); normals are an authorable input the generator can be handed, not assumed to be the geometric normal.
- **Failure mode — Muddy banding:** a toon ramp on geometric normals of noisy procedural meshes produces broken, flickering shadow lines (why hand-normals matter, and why procedural toon characters need a normal-smoothing pass).

## D.2 Outline rendering — pick the family by topology

**Inverted hull** (extrude a back-facing duplicate along normals) gives per-object width/color control and scales width with distance, but doubles polys, clips on hard edges, holes on concave meshes. **Post-process edge detection** (depth/normal discontinuity in screen space) never clips into geometry and catches more edges, but is noisy and needs multiple detectors. ✅ (Unreal Fest Japan 2023; Ronja; Kodeco.) No universally cheaper option — it depends on topology.

- **Test for:** the chosen method matches mesh topology — inverted hull only on meshes verified smooth/convex enough not to hole; post-process tuned with depth+normal detectors to suppress interior noise.
- **Failure modes — Inverted-hull holes** (gaps/spikes on hard-edged or concave generated meshes) · **post-process noise** (spurious interior edges on high-frequency surfaces).

## D.3 Treat stylization as a performance and readability decision

Reach for stylized/NPR when the budget is tight or readability at gameplay distance matters — flat shading, fewer texture samples, and banded lighting cost less than full PBR and make gameplay-critical elements easier to distinguish; stylized also ages better (doesn't chase advancing realism). ⚠️ (Industry consensus — N-iX, RocketBrush, VFX Apprentice — corroborated in *direction* by Xrd / Hi-Fi Rush at 60 FPS native, but "cheaper"/"ages better" are judgments, not measured.) → `art-direction-and-readability` owns the decision; this skill owns the technique.

- **Test for:** at gameplay camera distance, enemies/hazards/interactables are distinguishable by silhouette + signal color within a fixed glance time; the style hits its frame-rate target on min-spec.
- **Failure mode — Fidelity over legibility:** a render that dazzles in a close-up beauty shot but loses gameplay-critical elements in clutter at play distance.

**→ Procedural / headless implication.** Stylized pipelines are *more* generator-friendly: flat/banded materials have fewer parameters to get wrong, and a fixed signal palette keeps generated content readable by construction. The required human pass is normal authoring/smoothing for toon characters and topology-checking meshes before applying inverted-hull outlines.

---

# Sub-domain E — Performance & Failure Modes

> All three failures are machine-checkable: variant/PSO count, overdraw pixels-per-frame, concurrent-emitter/screen-coverage. They belong in a budget validator run on every generated batch — the rendering analogue of the juice-budget linter.

## E.1 Shader-variant / PSO explosion — cap combinatorics, precache the rest

Every keyword/feature toggle multiplies variant count; each unique combination of keywords + vertex format + blend mode + render target is a distinct variant (Unity) / PSO (Unreal). The first encounter compiles the PSO **on the frame's critical path** (a few–tens of ms) → a visible hitch. Cap the multiplier, precache hot PSOs at load/spawn, cut rare permutations. ✅ (Unreal tech blog; MJP permutation series; Unity — variant spaces reach millions; open worlds exceed 10,000 PSOs.)

- **Test for:** total shipped variant/PSO count is measured and bounded; a precache pass compiles hot PSOs before gameplay; no new PSO compiles in steady-state play.
- **Failure mode — Compilation stutter** (hitches the first time each new material/state appears); **root cause — keyword sprawl** (an über-shader whose toggle product is unshippable).

## E.2 Overdraw — bound transparent-layer depth and fill rate

Transparent elements (particles, UI, sprites) stack and shade the same pixel many times; particles "excel at creating overdraw". Mobile fill-rate ≈ pixels × shader complexity × overdraw, so even simple scenes go fill-rate-bound. Minimize stacked layers, use the simplest particle shaders, prefer fewer large particles, render dense systems off-screen at half-res. ✅ (Unity manual; TheGamedev.Guru; Android; GPU Gems 3 Ch.23.) → the cost mechanism behind `game-feel-and-juice` C.6's "particle storm".

- **Test for:** an overdraw debug view stays under a per-scene ceiling at peak effect load; particle shaders confirmed cheap (no per-pixel lighting on smoke); transparent layer depth capped.
- **Failure mode — Fill-rate collapse:** layered transparent VFX shading each pixel 10×+, dropping FPS on exactly the climactic moments that spawn the most effects.

## E.3 The particle storm — fails twice

Cap simultaneous on-screen VFX so effects communicate instead of obscure. The storm breaks frame rate (E.2) *and* hides the gameplay state the VFX was meant to convey. ⚠️ (Community/practitioner consensus, well-attested: *Tekken 7* effects "obscure character movements"; *Ori and the Will of the Wisps* "literally unreadable"; *Overwatch* the counter-example — "every effect remains readable even when dozens appear at once".) → this is `game-feel-and-juice` C.9's inverted-U violated in the VFX channel.

- **Test for:** at maximum simultaneous combat/ability load, gameplay-critical signals (enemy tells, hazard zones, player position) remain identifiable; concurrent emitters and screen-coverage under budget.
- **Failure mode — Spectacle over communication:** measuring VFX by quantity, saturating the screen so the player can't read the attack they have a split second to answer.

**→ Procedural / headless implication.** Build a **VFX/shader budget validator** for every generated batch: (1) variant/PSO count under cap with a precache manifest; (2) overdraw under the per-scene ceiling at simulated peak load; (3) concurrent-emitter and screen-coverage caps. Mirrors the juice-budget linter in `game-feel-and-juice` and routes failures back to the generator like `procgen-review`.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Per-pixel waste | Constant/per-vertex math in the fragment shader | Move work up to the vertex stage / a uniform (A.1) |
| Over-stuffed interpolators | Variant splits, exhausted slots | Pass only data that needs interpolation (A.2) |
| Unbounded parameter surface | Broken material at some parameter value | Document + range-bound every parameter (B.1) |
| UV dependence on UV-less geometry | Stretched/garbage texturing on voxel meshes | Triplanar / other UV-free path (B.2) |
| Procedural sameness | Every surface reads identical | Multiplicative param space + hero anchors (B.2) |
| Broken SDF field | Raymarcher misses surfaces / holes | smax-confine + smin-merge, not addition (B.3) |
| Wrong-engine particles | CPU storm, or GPU can't drive gameplay | Choose by count×coupling quadrant (C.1) |
| Strobing trail | Fast sprites read as a dotted line | Ribbon / velocity-stretched billboard (C.2) |
| Decal projection bleed | Decals on grazing/unintended surfaces | Clamp by surface-normal angle (C.3) |
| Grab-pass-per-effect | Bandwidth multiplied by distortion count | One shared scene-color capture per frame (C.4) |
| Rim everywhere | Nothing reads as special | Drive by uniform; reserve to signal palette (C.5) |
| Muddy toon banding | Flickering shadow lines on procgen meshes | Hand/smoothed normals; authorable normal input (D.1) |
| Inverted-hull holes | Gaps/spikes on hard-edged outlines | Match outline family to topology (D.2) |
| Fidelity over legibility | Great beauty shot, unreadable at play distance | Judge readability at gameplay distance first (D.3) |
| Compilation stutter | Hitches on new material/state combos | Cap variants; precache hot PSOs (E.1) |
| Fill-rate collapse | FPS drops under layered transparency | Overdraw budget; simplest particle shaders (E.2) |
| Particle storm | Unreadable, framerate-tanking spectacle | Cap concurrent VFX; signals stay readable (E.3) |

---

## Caveats

- **"Stylized is cheaper and ages better" is consensus, not measurement.** Unlike Kao's juiciness study, no controlled study establishes stylized < PBR on cost or longevity. The shipped exemplars (Xrd, Hi-Fi Rush, TF2, Wind Waker) corroborate the *direction* — high frame rate, durable readability — but production cost depends on the specific style (hand-painted PBR can be *more* labor than realistic PBR). A strong default, not a law.
- **The CPU/GPU particle FPS numbers are one study.** The *shape* (CPU cheap-then-cliff, GPU high-base-then-flat) is corroborated by Unreal's docs, but the crossover is engine/platform/effect-specific. Profile on target hardware; don't port the numbers.
- **Outline and cel techniques are mesh-topology-dependent.** They assume well-behaved normals and topology. Procedural meshes (marching cubes, voxel) frequently violate this — the single biggest place generated stylized content needs a hand-authored/automated cleanup pass; the source literature mostly assumes hand-authored meshes.
- **No raw shader code by design.** This skill gives data-flow, cost models, and test criteria only. The actual GLSL/HLSL/GDScript/Shader-Graph implementation belongs in the engine-hand packs (`godot`/`unreal`/`unity`/`threejs`), per the design-here/implement-there split in `docs/architecture.md`.
- **AI-authoring adds a risk the sources don't cover.** The shader/VFX literature assumes a human technical artist who *sees* the overdraw, variant count, and particle storm. A headless generator does not — the budget validators (E) are the substitute for that eye and must exist before generation is trusted; even then, procedural-material sameness (the oatmeal problem) needs early playtest spot-checks, not just automated bounds-checking.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
</content>
