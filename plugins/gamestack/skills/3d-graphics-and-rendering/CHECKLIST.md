# 3D Graphics & Rendering — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for the rendering layer. Run against a render-architecture decision, a lighting/post setup, or a batch of generated scenes. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** decide architecture + imaging contract first → then content → then performance budgets. Don't tune per-scene what should be a global contract; don't optimize before diagnosing the bound.

---

## A · The rasterization pipeline (respect stage limits)

**Do**
- [ ] Map each effect onto the **stage/pass** that can perform it; make neighbor-dependent effects (blur, AO, AA, GI gather) explicit full-screen passes with inputs listed.
- [ ] Emit a **render-pass graph** (which pass produces/consumes depth/normal/color buffers) as a fixed contract.
- [ ] Reach for **mesh/compute shaders only** for geometry-density / GPU-culling reasons, with a fallback + hardware-support check.

**Don't**
- [ ] Don't ask the fragment stage to read another fragment's output, sample arbitrary geometry, or emit vertices.
- [ ] Don't adopt a Nanite/mesh-shader pipeline to "fix" a fill-rate- or shader-bound scene.

**Test for** — Does every effect name its pipeline stage/pass? Does the choice of mesh shaders cite a geometry-density reason (not fill rate)? Is the pass graph explicit and stage-legal?

---

## B · PBR (energy conservation is the invariant)

**Do**
- [ ] Use a recognized **energy-conserving microfacet BRDF** (GGX + Smith + Fresnel).
- [ ] Default to **metallic/roughness**; document the workflow and channel packing once.
- [ ] Keep **metalness near-binary** (a mask), dielectric F0 ~2–5% unless metallic.
- [ ] Drive ambient/reflection with **image-based lighting** (split-sum: prefiltered cubemap + BRDF LUT); place reflection probes to cover the playable space.
- [ ] For an NPR target, author a **fixed parametric stylization contract** (band count, outline method, shadow source).

**Don't**
- [ ] Don't tune a material under one lighting setup only.
- [ ] Don't author mid-gray metalness except at genuine wear/rust edges.
- [ ] Don't mix PBR and NPR assets, or ship per-asset toon ramps with no shared spec.

**Test for** — Rendered under ≥3 distinct lighting environments, does each material read as the same physical substance? Is metalness near-binary? Does the NPR target have a documented parametric contract?

---

## C · Lighting & shadows (architecture is a frozen contract)

**Do**
- [ ] Choose **forward / deferred / clustered** from expected simultaneous light count, material variety, and MSAA/transparency needs; compute G-buffer memory before committing to deferred.
- [ ] Match **GI** (baked lightmaps · probes · voxel/SDF · ray-traced) to scene dynamism; prefer a baked/probe baseline + targeted dynamic detail; budget the per-frame cost on target hardware.
- [ ] Tune shadows with **slope-scaled depth bias** + tight near/far planes + texel-snapping; CSM for large directional scenes, bias per cascade.
- [ ] Soften shadow edges with **PCF** (budgeted kernel); keep softness consistent across cascades.

**Don't**
- [ ] Don't pick deferred then bolt on costly MSAA/transparency workarounds (consider clustered forward).
- [ ] Don't bake GI for runtime-generated geometry (you can't bake what doesn't exist at build time) — use probe/SDF/RT or a generation-time bake.
- [ ] Don't retune shadow bias / cascade splits per generated region.

**Test for** — Is the renderer choice justified by light count + MSAA/transparency need? Flat lit surface: no acne speckle? Contact shadows touch object bases (no peter-panning)? Edges stable on slow camera pan (texel-snapping)? Near shadows crisp + far present (CSM)?

---

## D · Performance & culling (diagnose the bound first)

**Do**
- [ ] **Profile CPU-bound vs GPU-bound** before any optimization; name the bound each task targets.
- [ ] **Frustum-cull** everywhere; add **occlusion culling** where large occluders justify it (profile it pays).
- [ ] Author **LOD** chains (or virtualized geometry) with resolution/FOV-tuned thresholds + pop-hiding (dither/cross-fade).
- [ ] **Instance** many-copy content; **batch** same-material static geometry; control **overdraw** with budgets + early-Z / front-to-back ordering.

**Don't**
- [ ] Don't instance a fill-bound (GPU) scene or add LOD to a draw-call-flooded (CPU) scene.
- [ ] Don't render the whole world every frame, or run occlusion culling where it costs more than it saves.
- [ ] Don't tune LOD thresholds for one resolution and ship at another.
- [ ] Don't stack translucent layers (alpha/foliage cards) without an overdraw-depth budget.

**Test for** — Does each optimization show a before/after frame-time delta against the measured bound? Off-frustum objects issue zero draw calls? Geometry behind a large occluder isn't shaded? Many-copy content uses instancing? Overdraw view within the budgeted depth?

---

## E · Post-processing (mandatory order, frozen imaging contract)

**Do**
- [ ] Run the chain **HDR effects (DoF, motion blur, bloom) → tonemap → LDR color grade → AA resolve → UI**; apply exposure before the tonemap curve.
- [ ] Use a **filmic tonemap** (ACES / Hable) applied **globally**; highlights roll off, shadows aren't crushed.
- [ ] Add **AO** (SSAO/HBAO/GTAO) with budgeted, halo-free falloff; pick **AA** matched to the renderer and document the blur-vs-shimmer-vs-cost tradeoff.
- [ ] When GPU-bound, recover budget with **dynamic resolution + temporal upscaling** (TAAU/DLSS/FSR/XeSS/TSR) fed by motion vectors — before cutting content.

**Don't**
- [ ] Don't bloom after tonemapping, grade in HDR, or apply exposure after the curve (unless a documented perf exception).
- [ ] Don't clamp raw HDR to LDR with no/naive tonemapping.
- [ ] Don't vary exposure / tonemap / AO falloff / bloom threshold / AA **per generated scene**.
- [ ] Don't use MSAA in a deferred renderer expecting it to cover shading aliasing.

**Test for** — Do bloom/DoF/motion-blur read pre-tonemap HDR? Is grading an LDR LUT post-tonemap? Is one filmic operator + exposure model shared by every scene? AO without dark halos? AA method matches the renderer?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Lighting architecture** (forward/deferred/clustered) → frozen; generator places lights within the per-cluster/tile budget, never switches architecture.
- [ ] **Imaging pipeline** (exposure · tonemap · AO falloff · bloom threshold · AA) → **imaging-contract guard** (generated scenes never override the globals).
- [ ] **Shadow constants** (slope-scaled bias band, CSM splits) → tuned once against worst-case generated geometry (thinnest walls, steepest slopes), frozen.
- [ ] **GI strategy** → probe/SDF/RT for runtime geometry, or a generation-time bake pass; never "bake the lighting" against runtime-generated content.
- [ ] **Draw-call + overdraw budget** → **budget linter** (reject scenes exceeding it).
- [ ] **Material contract** → chosen PBR workflow, energy-conserving, metalness near-binary, parameters within vetted ranges; NPR only via a fixed parametric contract.
- [ ] **Mesh validity** (with `procedural-geometry`) → watertight, correctly-wound, valid UVs before any virtualized-geometry clustering.

> For material & VFX authoring (shader model, particles, stylization mechanics) apply `shaders-and-vfx`; for readability/silhouette/signal-color apply `art-direction-and-readability`; for generated meshes/terrain apply `procedural-geometry`. Gate every generated batch through `procgen-review` as well.
