# 3D Graphics & Rendering — Guide

The discipline of assembling a real-time frame: the pipeline stages and their limits, the lighting architecture, the shadow/GI choices, the performance budget, and the imaging chain. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Akenine-Möller, Haines & Hoffman's *Real-Time Rendering* (4th ed.), Burley's Disney "principled" BRDF (SIGGRAPH 2012), Karis's UE4 PBR and Nanite deep-dives (SIGGRAPH 2013/2021), Olsson et al. on clustered forward shading (HPG 2012), Microsoft's shadow-depth-map techniques, Hable/Narkowicz on filmic tonemapping, and Motomura's Guilty Gear Xrd NPR talk (GDC 2015). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round3-technical/T1-3d-graphics-and-rendering.md`).

> **The central law:** *decide the architecture and the imaging contract once, then freeze them; everything else is a budget.* The renderer, the shading model, the shadow band, and the exposure→tonemap→grade chain are global contracts — not per-scene dials. Performance is triage: diagnose CPU-bound vs GPU-bound first, because the fixes are opposite.

> For material & VFX authoring (shader model, particles, stylization mechanics) see `shaders-and-vfx`; for readability/silhouette/signal-color see `art-direction-and-readability`; for generated meshes/terrain see `procedural-geometry`. This skill renders what those produce.

---

# Sub-domain A — The Rasterization Pipeline

> The real-time pipeline (*RTR4*) is a fixed sequence: application → geometry/vertex processing → rasterization → fragment/pixel processing → output-merger (depth test + blend). ✅ Each stage is massively parallel and *stateless across invocations* — a fragment shader sees one fragment, can't read a neighbor or other geometry, can't emit vertices. Knowing what a stage *can't* do is what prevents impossible asks.

## A.1 Map every effect onto the stage that can actually do it

Vertex/geometry stage transforms and may create/remove primitives; rasterization decides coverage and interpolates; the fragment stage shades one isolated fragment; the output-merger does depth + blend. Neighbor- or scene-dependent effects (blur, AO, AA, GI gather) are *separate full-screen passes*, not fragment-shader tricks. ✅ (*SSAO exists because* a fragment shader can't sample neighbor geometry — Crytek read a full depth buffer in a later pass, Crysis 2007.)

- **Test for:** every effect names the stage/pass it runs in; nothing asks the fragment stage to read another fragment's output, sample arbitrary geometry, or emit vertices; multi-sample effects are explicit passes with their inputs (depth/normal/prior-color) listed.
- **Failure mode — Stage-impossible asks:** "darken pixels near edges in the fragment shader" or "have the material spawn triangles" — silently becomes wrong or absurdly expensive. Relocate to the correct pass.

## A.2 Use mesh/compute shaders for geometry density, not as a default

The mesh-shader path (task/amplification → mesh → fragment) restructures only the geometry *front-end* — it emits small meshlets and enables per-cluster GPU culling. The fragment stage and output-merger are unchanged, so it does **not** fix fill-rate or overdraw. ✅ (*Nanite*, Karis SIGGRAPH 2021: ~128-tri clusters, GPU hierarchical-Z culling, sub-pixel LOD — a geometry revolution, not a shading one.)

- **Test for:** choosing mesh shaders cites a geometry-density / draw-call reason, confirms hardware support + a fallback, and is *not* invoked to fix a fill-rate- or light-bound problem.
- **Failure mode — Cargo-culted modern pipeline:** adopting Nanite-style geometry for an overdraw- or shader-bound scene; complexity and a hardware cliff with no frame-time win.

**→ Procedural / headless implication.** A generator emits a *render-pass graph* (which pass produces/consumes which buffers), a hand-authored contract it parameterizes within but never violates. Generated high-poly geometry pairs well with virtualized-geometry pipelines (automatic LOD removes per-asset LOD authoring) — but the mesh must still be watertight and correctly wound (→ `procedural-geometry`), since clustering amplifies topology bugs.

---

# Sub-domain B — Physically Based Rendering (PBR)

> **PBR's value is consistency, not realism.** ✅ A material that obeys energy conservation over a measured microfacet BRDF (Disney "principled," Burley 2012; UE4, Karis 2013; Frostbite, 2014) reads as the same substance under sky, torch, or noon sun. For a generator that consistency is the whole point — vetted parameters compose safely.

## B.1 Treat energy conservation as the invariant worth keeping

Use a microfacet BRDF (GGX distribution + Smith masking-shadowing + Fresnel) whose specular + diffuse never reflects more than it receives. That's what makes one material valid in any light. ✅ (Disney's model was fit to the MERL measured-material database, then simplified for artists; three major engines ship the same core.)

- **Test for:** the model is a recognized energy-conserving microfacet BRDF; each material is rendered under ≥3 distinct lighting environments and reads as the same physical substance in all three; dielectric F0 sits ~2–5% unless metallic.
- **Failure mode — "Looks right in one light only":** a material tuned under one setup that turns plasticky/glowing/muddy elsewhere — the signature of a non-conserving hack.

## B.2 Pick metallic/roughness vs specular/glossiness as a data + discipline call

Default to **metallic/roughness** — compact, fewer ways to author an invalid material, the dominant game workflow (UE, Godot, Unity URP/HDRP). Use specular/glossiness only when you need explicit dielectric specular color, and pay the extra memory + discipline to stay energy-conserving. Both feed the *same* BRDF — an encoding choice, not a quality one. ✅

- **Test for:** one workflow per project, documented; texture channels match it (metal+rough+AO packed); metalness is near-binary (a mask), not a mid-gray, except at genuine wear/rust edges.
- **Failure mode — Mixed / mid-gray metalness:** partial metalness on a surface that is either metal or not → muddy, wrong-energy results.

## B.3 Depart from PBR on purpose for stylized / NPR

When the art target is illustration (toon, painterly, graphic), replace the physical BRDF with a controlled, *consistent* response (banded/ramp shading, hand-authored normals, baked light) — and treat that as correct engineering, not a shortcut. ✅ (*Guilty Gear Xrd*, Motomura GDC 2015: hand-edited vertex normals, baked shadows, math accuracy rejected — more readable *and* cheaper than PBR for that target. → `shaders-and-vfx`, `art-direction-and-readability`.)

- **Test for:** an NPR target has a documented parametric shading contract (band count, outline method, how shadows are produced) so it stays consistent across assets.
- **Failure mode — Inconsistent stylization:** mixing PBR and NPR assets, or per-asset toon ramps with no shared spec → the scene reads as a clash of rendering models.

## B.4 Drive ambient/reflection with image-based lighting (split-sum)

Most of a material's cross-lighting "rightness" comes from the *environment* response, not direct lights. Pre-integrate it via the **split-sum approximation** — a roughness-mipmapped prefiltered cubemap + a 2D BRDF LUT (roughness × view angle) — so ambient specular is two texture fetches. Treat the environment/IBL as part of the imaging contract, shared across a scene's materials. ✅ (Karis, UE4 SIGGRAPH 2013, made full IBL real-time; standard in UE/Unity/Godot.)

- **Test for:** PBR scenes have a defined environment/IBL source (skybox + reflection probes); reflective and rough materials both read correctly against it; probes cover the playable space.
- **Failure mode — Missing/sparse IBL:** metallic surfaces look flat or black with nothing to reflect; or sparse probes show stale/wrong reflections.

**→ Procedural / headless implication.** PBR is the *more* generator-safe choice — energy conservation bounds the output, so a generator can vary base color / roughness / metallic within vetted ranges and stay plausible. NPR is generator-*hostile* unless the stylization is itself a fixed parametric contract; otherwise flag it as needing a human art pass per asset.

---

# Sub-domain C — Lighting & Shadows

## C.1 Choose the lighting architecture by light count, material variety, and MSAA/transparency needs

It constrains everything downstream, so decide up front. **Forward:** few lights, many materials, MSAA + transparency free. **Deferred:** many lights, uniform materials, but the fat G-buffer makes MSAA expensive (a 1080p 16×MSAA HDR G-buffer exceeds ~250 MB) and transparency awkward. **Forward+/clustered:** many lights *and* MSAA/transparency, at the cost of a light-binning pre-pass. ✅ (Olsson et al. HPG 2012: clustered forward ~161 FPS vs tiled deferred ~53 FPS at 8×MSAA 720p; VR favors forward for the bandwidth/MSAA reason.)

- **Test for:** the choice is justified by expected simultaneous light count, material diversity, and whether MSAA + ordered transparency are required; G-buffer memory is computed for target resolution before committing to deferred.
- **Failure mode — Deferred-then-fighting-MSAA:** choosing deferred for many lights, then needing crisp edges + transparency and bolting on costly workarounds — when clustered forward gives all three.

## C.2 Match the GI approach to scene dynamism, and budget it

Place each scene on the static↔dynamic axis and pick the cheapest GI that covers it. **Baked lightmaps:** ~free at runtime, static-only, storage-heavy. **Light probes:** cheap dynamic-object GI, low spatial resolution. **Voxel/SDF GI** (Godot SDFGI, DDGI lineage ⚠️): fully dynamic, no special hardware, higher memory/streaming. **Ray-traced GI** (DDGI / Lumen HW): highest fidelity, trades light-leak for sampler noise + denoiser blur, costliest. Prefer **hybrid**: baked/probe baseline + targeted dynamic detail. ✅ (Lumen SIGGRAPH data: surface-cache reflections 2.44 ms vs hit-lighting 11.54 ms PS5 — "ray traced" spans ~5× cost by itself ⚠️.)

- **Test for:** each scene's GI is justified by its dynamism; baked content has a lightmap budget; dynamic GI has a measured per-frame cost on target hardware; light-leak/noise checked at shipping quality, not editor preview.
- **Failure mode — Mismatch:** ray-traced GI on a scene that never changes (bake it), or baked GI on a scene whose lighting must change (it goes stale).

## C.3 Tune shadow bias inside the narrow acne↔peter-panning band

Apply **slope-scaled depth bias** (more bias on surfaces edge-on to the light, near-zero facing it), keep near/far planes tight for depth precision, and snap the shadow projection to texel increments to kill edge shimmer. Too little bias → **shadow acne** (self-shadow speckles/moiré from depth quantization); too much → **peter-panning** (shadow detaches, object floats). There is no single global bias. ✅ (Microsoft, "Common Techniques to Improve Shadow Depth Maps"; front-face culling in the shadow pass reduces acne but worsens peter-panning on thin geometry.)

- **Test for:** at shipping resolution a flat lit surface shows no speckle (no acne) *and* contact shadows touch object bases (no peter-panning); edges don't shimmer on slow camera translation (texel-snapping works).
- **Failure modes — Shadow acne** (too little bias / precision) · **Peter-panning** (too much bias) · **Edge shimmer** (projection re-fit each frame without snapping).

## C.4 Use cascaded shadow maps to fight perspective aliasing over distance

For large outdoor scenes with a sun (directional) light, split the view frustum into distance cascades — crisp near, blurry-and-cheap far — and tune bias *per cascade*, minimizing transition seams. ✅ (CSM is "the most popular technique for perspective aliasing," *RTR4* / Microsoft Learn; standard in every open-world directional-shadow setup.)

- **Test for:** near shadows crisp, far shadows present (no hard cutoff); cascade boundaries show no obvious quality "wall"; per-cascade bias set independently.
- **Failure mode — Single-map directional shadows at scale:** crisp-near-no-far or blurry-everywhere — the tell that cascades are missing or mis-split.

## C.5 Filter shadow edges (PCF) to trade hard aliasing for controlled softness

Soften the raw shadow comparison with **percentage-closer filtering** (average the depth test across a small kernel), or a contact-hardening variant (PCSS, penumbra grows with occluder distance). Budget the kernel — bigger = more samples/pixel. ✅ (Microsoft Learn recommends PCF/VSM to soften edges; every shipped engine exposes a PCF quality setting.)

- **Test for:** shadow edges are softened (no single-texel stair-stepping) at shipping quality; the kernel/sample count is a budgeted, documented setting; softness is consistent across cascades.
- **Failure mode — Hard aliased shadow edges:** unfiltered comparison gives blocky, crawling borders under camera motion.

**→ Procedural / headless implication.** The lighting *architecture*, shadow bias, and cascade splits are **fixed global constants**, never per-generated-scene values — retuning them per region produces inconsistent acne/peter-panning across the world. A generator places lights within the architecture's per-cluster/tile budget; it does not switch architectures or retune bias. Baked GI is **incompatible with runtime-generated geometry** (you can't bake what doesn't exist at build time) — procedural worlds bias toward probe/SDF/RT GI, or a *generation-time* bake step. Flag any "bake the lighting" recommendation against a runtime-generated world.

---

# Sub-domain D — Performance & Culling

> **"Quality" problems are usually budget problems.** Culling, LOD, batching/instancing, and overdraw control decide whether the frame fits at all. The fixes for CPU-bound and GPU-bound are *opposite*, so diagnose the bound first. ✅

## D.1 Diagnose CPU-bound vs GPU-bound before optimizing anything

CPU-bound (draw-call submission, logic, culling cost) → fewer draw calls (batch/instance), less per-object work. GPU-bound (fill rate, overdraw, shader/vertex cost) → LOD, culling, simpler shaders, lower/scaled resolution. ✅ (Standard optimization decision tree; modern APIs reduce but don't remove per-draw CPU cost.)

- **Test for:** every optimization task names the measured bottleneck and shows a before/after frame-time delta; nothing ships as "optimization" without a profile proving it hit the actual bound.
- **Failure mode — Optimizing the wrong side:** instancing an overdraw-bound (GPU) scene, or adding LOD to a draw-call-flooded (CPU) scene — effort with no win.

## D.2 Cull aggressively: frustum first, then occlusion

Don't submit what won't be seen. Frustum-cull everything outside the camera volume (cheap, always on); add occlusion culling where large occluders hide significant geometry. Culling cuts both CPU (draw calls) and GPU (shading). ✅ (Nanite folds frustum + occlusion + small-feature culling into one GPU hierarchical-Z pass; classic engines use CPU frustum + portal/PVS or GPU occlusion queries.)

- **Test for:** off-frustum objects issue no draw calls; geometry fully behind a large occluder is measurably not shaded; occlusion culling's own cost is less than it saves (it can lose on open vistas with few occluders — profile).
- **Failure mode — No/insufficient culling:** rendering the whole world every frame; or occlusion culling that costs more than it saves in the open.

## D.3 Use LOD to bound cost, and hide the pop

Swap distant meshes for cheaper versions (fewer triangles, simpler shaders) so cost scales with screen size, not world size; manage pop-in with hysteresis, dither/cross-fade, or continuous LOD. ✅ (Discrete LOD: 50k-tri → 5k mid → 500 far saves ~99% of triangle work at distance; Nanite's sub-pixel-error LOD removes visible popping.)

- **Test for:** distant objects use reduced LODs; transitions don't visibly pop at normal distance/speed; LOD thresholds are tuned to target resolution/FOV (a 1080p threshold pops at 4K).
- **Failure mode — Pop-in / wrong thresholds:** objects snapping to detail as you approach, or thresholds tuned for one resolution breaking at another.

## D.4 Cut draw calls (batch/instance) and control overdraw (fill rate)

Merge draw calls — static batching for non-moving same-material meshes, GPU instancing for many copies of one mesh (foliage, crowds, debris). Separately control **overdraw** — shading the same pixel repeatedly, the dominant fill cost, worst with stacked transparency. ✅ (Overdraw's worst case is layered alpha/foliage cards over the same pixels — the canonical fill-rate killer; cross-ref `shaders-and-vfx`'s "particle storm".)

- **Test for:** many-copy content renders via instancing, not per-object draws; same-material static geometry is batched; an overdraw debug view shows transparent layers within a budgeted depth; opaque geometry uses early-Z / front-to-back ordering.
- **Failure modes — Draw-call flood** (thousands of individual submissions → CPU-bound) · **Overdraw blowout** (stacked alpha/foliage → GPU fill-bound).

**→ Procedural / headless implication.** Generators hit *both* failure modes — scattering thousands of unique objects (fix: instance identical scatter, → `procedural-geometry` blue-noise placement) and layering translucent VFX without a budget. Encode draw-call and overdraw budgets as validators a generated scene must pass before commit — mirror the feedback-budget linter from `game-feel-and-juice`.

---

# Sub-domain E — Post-Processing

> The post chain has a **mandatory order** and a **frozen imaging contract**. ✅ HDR optical effects run before tonemapping; tonemap once; grade in LDR. Exposure, tonemap, AO falloff, bloom threshold, and AA are global constants — never per-scene dials.

## E.1 Run the chain in HDR → tonemap → LDR order

Optical, HDR-space effects (depth of field, motion blur, bloom) run *before* tonemapping (they need true high-range light values); tonemap maps HDR→LDR once; color grading/correction runs *after*, in LDR (it's an artistic LUT over display-range colors); AA resolve + UI come last. Exposure multiplies *before* the tonemap curve. ✅ (renderingevolution.net; MJP "A Closer Look at Tone Mapping"; corroborated across engines.)

- **Test for:** bloom/DoF/motion-blur read pre-tonemap HDR color; grading is an LDR LUT post-tonemap; exposure is applied before the curve; any reorder (e.g. bloom post-tonemap for perf) is a documented exception.
- **Failure mode — Wrong post order:** bloom after tonemap (bright sources stop blooming), grading in HDR (unpredictable), exposure after the curve (loses its shaping).

## E.2 Tonemap with a filmic curve and keep exposure/tonemap global

Map HDR→display with a filmic S-curve (ACES, Hable/Uncharted-2, or engine default) that shapes the toe (shadows) and shoulder (highlights) — not a naive clamp or pure Reinhard if you want filmic contrast. Pick *one* tonemapper + exposure model for the whole game. ✅ (Hable's Uncharted-2 curve gives independent toe/shoulder control vs Reinhard's uniform x/(1+x); Narkowicz's ACES fit is the UE4 default; Reinhard desaturates/compresses uniformly.)

- **Test for:** a filmic (or deliberately chosen) operator applied globally; highlights roll off (no hard white clip) and shadows aren't crushed; the same exposure/tonemap config across every scene.
- **Failure mode — No/naive tonemapping:** raw HDR clamped to LDR blows highlights flat and crushes shadows; or per-scene tonemap/exposure makes one asset look like different materials between areas.

## E.3 Choose AO and AA for their specific artifacts, not by name

**AO:** add screen-space AO (SSAO/HBAO/GTAO) to ground contact shadows in creases; budget it and tune falloff to avoid dark halos. ✅ (SSAO — Kajalin/Crytek, Crysis 2007 — cheap but noisy with halos; HBAO — NVIDIA 2008 — marches the depth buffer, smoother, costlier; wrong falloff → black halos.)
**AA:** pick by tolerable artifact — **MSAA** (sharpest, geometry-edges-only, expensive/ineffective in deferred), **FXAA** (cheap, blurs), **TAA** (kills shimmer but ghosts + softens), **SMAA** (sharper than FXAA, mild ghosting). ✅ (TAA is the de-facto default in UE4/5, Unity HDRP, id Tech 7 — at the cost of blur + ghosting behind fast motion; MSAA "can't cover the majority of aliasing in deferred"; TAA lineage → Karis SIGGRAPH 2014.)

- **Test for (AO):** AO darkens creases/contacts without ringing dark halos; within budget at target resolution. **(AA):** the method matches the renderer (MSAA realistically only in forward; deferred → temporal/post AA) and the blur-vs-shimmer-vs-cost tradeoff is documented.
- **Failure modes — AO halos** (falloff too large) · **TAA ghosting/blur** (smear behind motion — needs motion vectors + per-game tuning) · **MSAA in deferred** (misses shading aliasing, huge bandwidth).

## E.4 Recover budget with dynamic resolution + temporal upscaling, not by cutting content

When GPU-bound, render at a lower internal resolution and reconstruct to display res with a temporal upscaler (TAAU, DLSS, FSR, XeSS, UE5 TSR) before the final AA/UI composite; dynamic resolution scales internal res per frame to hold the budget. Highest-leverage, content-preserving budget lever — but shares TAA's ghosting risk and needs motion vectors. ✅ (Standard in UE5/Unity/console DRS; same temporal lineage as TAA, Karis 2014.)

- **Test for:** if GPU-bound, an internal-res-scale + upscaler path exists before AA/UI; motion vectors feed it; reconstruction checked for ghosting/disocclusion on fast motion, not just static frames.
- **Failure mode — Brute-forcing native res:** cutting visible content to fit budget when an upscaler would preserve both; or an upscaler with no motion vectors → heavy smear.

**→ Procedural / headless implication.** Post-processing is the strongest case for a **fixed, hand-tuned pipeline contract**: exposure, tonemapping, AO falloff, bloom threshold, and AA are *global constants*, never per-generated-scene parameters. Vary them and identical content reads as visually incoherent across the world though each asset is individually correct. The generator varies *content*; the *imaging pipeline* is frozen — the rendering analogue of `game-feel-and-juice`'s frozen feedback-budget table.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Stage-impossible ask | Fragment shader told to read neighbors / spawn geometry | Relocate to the correct pass (A.1) |
| Cargo-culted mesh pipeline | Nanite/mesh shaders on a fill-bound scene | Use only for geometry density; profile first (A.2) |
| Looks right in one light only | Material plasticky/glowing elsewhere | Energy-conserving BRDF; test in ≥3 lights (B.1) |
| Mid-gray metalness | Muddy, wrong-energy surfaces | Metalness near-binary mask (B.2) |
| Missing/sparse IBL | Metallic surfaces flat/black; stale reflections | Environment + reflection probes, split-sum (B.4) |
| Inconsistent stylization | PBR + NPR assets clash | Fixed parametric NPR contract (B.3) |
| Deferred-then-fighting-MSAA | Costly edge/transparency workarounds | Choose by light count + MSAA need; consider clustered forward (C.1) |
| GI/dynamism mismatch | RT GI on a static scene, or stale baked light | Match GI to scene dynamism (C.2) |
| Shadow acne | Speckled self-shadowing | Slope-scaled bias + tight near/far (C.3) |
| Peter-panning | Shadows detached, objects float | Less bias; the narrow band (C.3) |
| Single-map directional shadows | Crisp-near-no-far or blurry-everywhere | Cascaded shadow maps (C.4) |
| Hard aliased shadow edges | Blocky, crawling shadow borders | PCF edge filtering (C.5) |
| Optimizing the wrong side | Instancing a fill-bound scene | Diagnose CPU- vs GPU-bound first (D.1) |
| No/insufficient culling | Whole world rendered every frame | Frustum then occlusion culling (D.2) |
| Pop-in / wrong LOD thresholds | Objects snap to detail; breaks at 4K | LOD + pop-hiding, resolution-tuned (D.3) |
| Draw-call flood | Thousands of individual submissions | Batch + instance (D.4) |
| Overdraw blowout | Stacked alpha/foliage tanks FPS | Overdraw budget + early-Z (D.4) |
| Wrong post order | Bloom after tonemap; grade in HDR | HDR effects → tonemap → LDR grade (E.1) |
| No/naive tonemapping | Blown highlights, crushed blacks | Global filmic curve + exposure (E.2) |
| AO halos / TAA ghosting | Dark rings; smear behind motion | Tune AO falloff; motion-vector TAA (E.3) |
| Brute-forcing native res | Cutting content to fit GPU budget | Dynamic resolution + temporal upscaling (E.4) |

---

## Caveats

- **Engine docs/talks are tradeoff sources, not gospel.** Vendor material (Unreal/Unity/Godot docs, NVIDIA/Epic talks) favors its own features. The *principles* (energy conservation, the post order, the acne/peter-panning band) are cross-corroborated; specific cost numbers (Lumen 2.44 vs 11.54 ms; clustered-forward 161 FPS) are single-platform single-benchmark ⚠️ — treat as illustrative ratios, not portable constants.
- **Real-time vs offline/film.** Everything here is *real-time rasterization*. Offline path tracing solves the same physics by brute-force ray integration — no shadow-bias band, no G-buffer choice, no screen-space AO fake. Don't import offline assumptions (unlimited bounces, no fill budget) into a real-time spec.
- **The GI/geometry frontier moves fast.** Virtualized geometry (Nanite), software/hardware Lumen, and ML upscalers/denoisers (DLSS/FSR/XeSS) evolve quickly; the forward/deferred/clustered taxonomy and PBR/post fundamentals are stable, but "what's affordable" at the GI/AA frontier shifts — re-verify cost claims against current hardware.
- **PBR parameter ranges are conventions, not laws.** "F0 ≈ 2–5%," "metalness near-binary" are strong defaults; artists break them deliberately. The energy-conservation *invariant* is the hard rule; the ranges are guidance.
- **NPR generation is under-served by the literature.** PBR research is deep; principled, generator-safe *stylized* rendering is thinner and bespoke. For an AI-authored stylized game the stylization contract needs real playtest/art review — flag as a risk, not solved (→ `shaders-and-vfx`, `art-direction-and-readability`).

---

*Expand with your own profiling data and citations. See CONTRIBUTING.md.*
