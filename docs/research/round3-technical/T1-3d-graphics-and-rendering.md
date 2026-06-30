# T1 — 3D Graphics & Rendering: Technical-Craft Research

> **Research document** — seed for the `3d-graphics-and-rendering` technical-craft skill.
> Written by the gamestack research agent; adversarially verified against independent sources.
> Engine- and genre-agnostic, **design/spec level** — principles, cost models, and "Test for:"
> criteria, **not engine code** (no GLSL/HLSL/GDScript). Verification tags:
> ✅ ≥2 independent/primary sources · ⚠️ single source · ❌ refuted or substantially contested.

---

## 1. TL;DR

- **The rasterization pipeline is a fixed sequence of stages, each with hard limits.** Vertex → primitive assembly → rasterize → fragment → output-merge. A fragment shader cannot see its neighbors or other geometry; a vertex shader cannot create geometry. Knowing what each stage *can't* do is what prevents impossible asks. Mesh/compute shaders break the front of the pipeline open but don't change the back. ✅
- **PBR's value is consistency, not realism.** A metallic/roughness material authored once reads "correct" across every lighting condition because it obeys energy conservation and a measured BRDF (Disney's "principled" model, 2012). For a generator, that consistency is the whole point: vetted material parameters compose safely; ad-hoc per-asset hacks do not. ✅
- **Lighting architecture is a triage decision, not a quality dial.** Forward, deferred, and forward+/clustered each trade light count against MSAA, transparency, and bandwidth. Deferred scales many lights but fights MSAA and transparency; clustered forward recovers both and, in one benchmark, ran ~3× faster than tiled deferred at 8× MSAA. Pick by light count and material variety, then live with the constraint. ✅
- **Most rendering "quality" problems are budget problems.** Culling (frustum/occlusion), LOD, batching/instancing, and overdraw control are not optional polish — they decide whether the frame fits at all. The first diagnostic is always CPU-bound vs GPU-bound, because the fixes are opposite. ✅
- **The post-processing chain has a mandatory order and a fixed exposure/tonemapping contract.** HDR effects (DoF, motion blur, bloom) run *before* tonemapping; color grading runs *after*, in LDR. For generated scenes, exposure and tonemapping must be a frozen, global contract — vary them per scene and the content reads as visually incoherent even when each asset is individually correct. ✅

---

## 2. Key Findings

**1. The rasterization pipeline's stage boundaries define what is renderable.** ✅
The canonical real-time pipeline (Akenine-Möller, Haines, Hoffman, *Real-Time Rendering* 4th ed., 2018) is: application → geometry processing (vertex shading, projection, clipping) → rasterization (which fragments a primitive covers) → pixel/fragment processing → merging (depth test + blend in the output-merger / ROP). Each stage is massively parallel and *stateless across invocations*: a fragment shader sees one fragment and cannot read another fragment's result, cannot see other triangles, and cannot create geometry. This is why effects that need neighbor information (blur, SSAO, AA) are *screen-space post-passes*, not fragment-shader tricks.

**2. Mesh and compute shaders restructure the front of the pipeline but not the merge stage.** ✅
Mesh shaders (NVIDIA Turing 2018; now in DX12/Vulkan) replace the vertex→tessellation→geometry front-end with a compute-style model: an optional task/amplification shader dispatches mesh shader workgroups that emit small "meshlets" (e.g. ~64–126 verts / ~128 triangles) directly to the rasterizer, enabling GPU-driven culling per cluster. The fragment stage and output-merger are unchanged. Nanite (UE5) is the shipped proof: hierarchical 128-triangle clusters, GPU culling via a hierarchical Z-buffer, sub-pixel LOD selection.

**3. PBR "looks right" because it enforces energy conservation over a measured microfacet BRDF.** ✅
Brent Burley's "Physically-Based Shading at Disney" (SIGGRAPH 2012) defined the "principled" BRDF: a Cook-Torrance microfacet specular (GGX/GTR distribution + Smith masking-shadowing + Schlick Fresnel) plus a diffuse lobe, with a small set of artist-friendly, normalized parameters (base color, metallic, roughness, etc.). Energy conservation — outgoing light never exceeds incoming — is what makes one material read correctly under sky, torch, or noon sun. Karis adapted it to real time for Unreal Engine 4 (SIGGRAPH 2013), and Frostbite (Lagarde & de Rousiers, SIGGRAPH 2014) independently corroborated the model.

**4. Metallic/roughness and specular/glossiness are two encodings of the same physics.** ✅
Both workflows feed the same BRDF. Metallic/roughness (UE4, Godot, Unity URP default) stores base color + a metallic mask + roughness — compact, fewer ways to author an energy-violating material, the dominant game workflow. Specular/glossiness stores an explicit specular color + glossiness — more control (e.g. distinct dielectric specular tints) but more memory and more ways to break energy conservation. The choice is a data-and-discipline tradeoff, not a quality one.

**5. Image-based lighting (the split-sum approximation) is what makes PBR ambient/reflection affordable in real time.** ✅
Most of a PBR material's "looks right everywhere" comes from the *environment* response, not direct lights. Karis (UE4, SIGGRAPH 2013) made this real-time via the **split-sum approximation**: pre-integrate the environment into a roughness-mipmapped prefiltered cubemap, and pre-integrate the BRDF into a small 2D lookup texture (the "BRDF LUT") indexed by roughness and view angle. Ambient specular then becomes two cheap texture fetches instead of a per-pixel integral. This is why a metallic surface reflects its surroundings plausibly under any sky — and why the environment/IBL setup is part of the frozen imaging contract, not a per-material detail.

**6. Stylized / NPR rendering departs from PBR deliberately, and is often cheaper and more readable.** ✅
Guilty Gear Xrd (Junya C. Motomura, GDC 2015) rebuilt a 2D-fighter look in full 3D by *rejecting* mathematical lighting accuracy: hand-edited vertex normals, baked rather than dynamic shadows, manual control of every shaded band. The point is that PBR is a *choice* — when the art target is illustration, deviating from physical correctness is correct. (Cross-reference `shaders-and-vfx` for the shading-model mechanics and `art-direction-and-readability` for the readability case.)

**7. Forward vs deferred vs forward+/clustered is a light-count-vs-everything-else tradeoff.** ✅
Deferred shading writes a G-buffer (albedo, normal, roughness, depth…) then lights once per pixel, decoupling lighting cost from geometry — great for many lights, but the fat G-buffer makes MSAA expensive (a 1080p 16×MSAA HDR G-buffer exceeds ~250 MB) and transparency awkward (one fragment per pixel). Forward lights geometry directly, gets MSAA and transparency for free, but scales poorly with light count. Forward+/clustered (Harada 2012; Olsson et al. HPG 2012) bins lights into screen tiles or view-space clusters so forward can carry many lights while keeping MSAA and transparency — Olsson's data showed clustered forward at ~161 FPS vs tiled deferred ~53 FPS at 8×MSAA 720p on a GTX 680.

**8. Real-time GI is a spectrum from "free but static" to "dynamic but expensive."** ✅
Baked lightmaps: near-zero runtime cost, but static-only and storage-heavy. Light probes: cheap dynamic-object GI via sampled irradiance, low spatial resolution. Voxel/SDF GI (e.g. Godot's SDFGI, derived from signed-distance-field DDGI): fully dynamic, no special hardware, higher memory/streaming cost. Ray-traced GI (DDGI/irradiance fields, Lumen's hardware path): highest fidelity, replaces light-leak with sampler noise + denoiser blur, and is the most expensive (Lumen SIGGRAPH data: hit-lighting 11.54 ms vs surface-cache 2.44 ms for the same reflections on PS5). Most shipped games are *hybrid*: a stable baked/probe baseline plus targeted dynamic passes.

**9. Shadow maps fail in two opposite, named ways, fixed by opposite knobs.** ✅
Shadow acne (erroneous self-shadowing) comes from depth quantization/precision: a surface shadows itself in a moiré of speckles. Peter-panning comes from *over*-correcting that with too much depth bias: the shadow detaches and the object floats. The fix is a narrow band — slope-scaled depth bias (more bias on surfaces edge-on to the light, none facing it), tight near/far planes for depth precision, optional normal offset, and texel-snapping to stop edge shimmer (Microsoft, "Common Techniques to Improve Shadow Depth Maps"). Cascaded shadow maps (CSM) address a third artifact — perspective aliasing — by using higher-resolution cascades near the camera.

**10. The first performance question is always CPU-bound vs GPU-bound, because the fixes are opposite.** ✅
CPU-bound (too many draw calls, heavy game logic) → cut draw calls via batching/instancing, pool objects, reduce per-object overhead. GPU-bound (fill rate, overdraw, shader cost) → add LOD, enable culling, simplify shaders, lower/scale resolution. Applying the wrong fix wastes effort: instancing a GPU-bound fill-rate scene does nothing. Diagnose before optimizing.

**11. The post-processing chain has a physically-motivated mandatory order.** ✅
HDR-space effects that simulate optics — depth of field, motion blur, bloom — must run *before* tonemapping (they operate on the true high-range light values). Tonemapping maps HDR→LDR. Color grading/correction runs *after*, in LDR, because it's an artistic LUT over display-range colors. AA resolve and UI come last. Exposure multiplies *before* tonemapping. Get the order wrong (e.g. bloom after tonemap) and bright sources stop blooming correctly.

---

## 3. Details by Sub-domain

---

### Sub-domain A — The Rasterization Pipeline

**Foundation text:** Akenine-Möller, Haines, Hoffman, *Real-Time Rendering* (4th ed., CRC Press, 2018), the standard reference for the real-time pipeline.

#### A.1 Respect each pipeline stage's hard boundaries

**Rule:** Map every rendering feature onto the stage that can actually do it. Vertex/geometry stage transforms and may create/remove primitives (tessellation, geometry shader, mesh shader); rasterization decides fragment coverage and interpolates; the fragment stage shades one fragment with no knowledge of neighbors or other geometry; the output-merger does the depth test and blend. If a feature needs information a stage doesn't have, it belongs in a different pass.

- **Exemplar:** Screen-space ambient occlusion exists *because* a fragment shader can't sample neighboring geometry — SSAO (Crytek, Crysis 2007) reads a full depth buffer in a later post-pass, which is the only stage where neighbor depth is available. The pipeline boundary *forced* the screen-space technique.
- **Test for:** every requested effect names the pipeline stage(s) it runs in; no spec asks the fragment stage to read another fragment's output, sample arbitrary scene geometry, or emit vertices. Multi-sample/neighbor-dependent effects (blur, AO, AA, GI gather) are specified as separate full-screen passes with their inputs (depth, normals, prior color) listed.
- **Failure mode — Stage-impossible asks:** specifying "make the fragment shader darken pixels near edges" or "have the material spawn extra triangles" — these silently become either wrong or absurdly expensive workarounds. The fix is to relocate the effect to the correct stage/pass.

**→ Procedural / headless implication.** A generator must emit a *render-pass graph*, not just materials: which pass produces depth/normal/color buffers and which consume them. The pass graph is a hand-authored contract; the generator parameterizes inputs within it but never invents a pass that violates stage boundaries.

#### A.2 Use mesh/compute shaders where geometry density or GPU-driven culling dominates — not by default

**Rule:** Reach for the mesh-shader path (task/amplification → mesh → fragment) when triangle counts and per-cluster culling dominate the frame; otherwise the classic vertex path is simpler and universally supported. Mesh shaders restructure only the geometry front-end; the fragment stage and output-merger are unchanged, so they don't fix fill-rate or overdraw problems.

- **Exemplar:** Nanite (Brian Karis, "Nanite: A Deep Dive," SIGGRAPH 2021) clusters meshes into ~128-triangle groups, culls them on-GPU with a hierarchical Z-buffer (frustum + occlusion + small-feature in one pass), and selects sub-pixel-error LODs — lifting the "frame budget is bounded by polycount/draw calls" constraint. It is a geometry-front-end revolution, not a shading one.
- **Test for:** the choice to use mesh shaders cites a geometry-density or draw-call-count reason and confirms target-hardware support + a fallback path; a fill-rate-bound or light-count-bound problem is *not* "solved" by switching to mesh shaders.
- **Failure mode — Cargo-culted modern pipeline:** adopting mesh shaders / Nanite-style geometry for a scene that's overdraw- or shader-bound, gaining complexity and a hardware-support cliff with no frame-time win.

**→ Procedural / headless implication.** Generated high-poly geometry (e.g. from `procedural-geometry`) pairs naturally with virtualized-geometry pipelines because automatic clustering/LOD removes the need for a human to author LOD chains per asset — but the generator must still emit watertight, correctly-wound meshes (see `procedural-geometry`), since clustering amplifies topology bugs across LODs.

---

### Sub-domain B — Physically Based Rendering (PBR)

**Foundation texts:** Burley, "Physically-Based Shading at Disney" (SIGGRAPH 2012 PBS course); Karis, "Real Shading in Unreal Engine 4" (SIGGRAPH 2013); Lagarde & de Rousiers, "Moving Frostbite to PBR" (SIGGRAPH 2014).

#### B.1 Treat energy conservation as the invariant that makes PBR worth using

**Rule:** Use a PBR material model whose specular + diffuse response never reflects more light than it receives, evaluated with a microfacet BRDF (GGX distribution, Smith masking-shadowing, Fresnel). This is what lets a single material read correctly under any lighting; abandon it only deliberately (NPR, see B.3).

- **Exemplar:** The Disney principled BRDF (Burley 2012) was validated against the MERL measured-material database — the parameters were chosen to fit *measured* reflectance, then simplified for artists. UE4 (Karis 2013) and Frostbite (2014) shipping the same core model is the cross-engine corroboration.
- **Test for:** the lighting model is a recognized energy-conserving microfacet BRDF; materials are validated by rendering them under ≥3 distinct lighting environments (e.g. bright outdoor HDRI, dim interior, single point light) and confirming they read as the same physical substance in all three. Dielectric base reflectance (F0) sits in the physically plausible ~2–5% range unless metallic.
- **Failure mode — "Looks right in one light only":** a material hand-tuned under one lighting setup that turns plasticky, glowing, or muddy elsewhere — the signature of a non-energy-conserving or ad-hoc shading hack.

#### B.2 Pick metallic/roughness vs specular/glossiness as a data + discipline decision

**Rule:** Default to **metallic/roughness** for game content — it is compact and has fewer ways to author an invalid material. Use specular/glossiness only when you specifically need explicit per-pixel dielectric specular color, and accept the extra memory and the extra discipline required to keep it energy-conserving.

- **Exemplar:** Metallic/roughness is the default in UE4/UE5, Godot, and Unity URP/HDRP standard materials; specular/glossiness survives mainly in asset pipelines that need it (some character/hard-surface workflows). Both feed the identical BRDF — this is an encoding choice, confirmed across all three major engines' docs.
- **Test for:** one workflow is chosen per project and documented; texture channels match it (metallic-roughness packs metal + rough + AO into one RGB texture); no material mixes conventions. A "metalness in [0,1], not a mid-gray" check catches the most common authoring error.
- **Failure mode — Mixed/!0-or-1 metalness:** partially-metallic base values (metalness 0.5 on a surface that is either metal or not) produce muddy, wrong-energy results; the value should be a near-binary mask except at genuine transition edges (rust, wear).

#### B.3 Depart from PBR on purpose for stylized/NPR targets

**Rule:** When the art direction is illustrative (toon, painterly, graphic), replace the physical BRDF with a controlled, readable response (banded/ramp shading, hand-authored normals, baked light) — and treat that as the *correct* engineering choice, not a shortcut. Keep the departure *consistent* so the world still reads as one place.

- **Exemplar:** Guilty Gear Xrd (Motomura, GDC 2015) — vertex normals hand-edited so cel bands fall where the artist wants from any angle; dynamic shadows replaced with baked ones; mathematical accuracy explicitly rejected. The result is more readable *and* cheaper than PBR for that target.
- **Test for:** an NPR target has a documented, parametric shading contract (how many light bands, outline method, how shadows are produced) so it stays consistent across assets; "stylized" never means "each asset shaded ad-hoc."
- **Failure mode — Inconsistent stylization:** mixing PBR and NPR assets, or per-asset toon ramps with no shared spec, so the scene reads as a clash of rendering models rather than one art direction.

#### B.4 Drive ambient/reflection with image-based lighting, pre-integrated for real time

**Rule:** Light PBR materials' ambient and reflection response from an environment (IBL), pre-integrated via the **split-sum approximation** — a roughness-mipmapped prefiltered environment cubemap plus a 2D BRDF lookup texture indexed by roughness and view angle. Ambient specular becomes two texture fetches, not a per-pixel integral. Treat the environment/IBL as part of the imaging contract, shared across materials in a scene.

- **Exemplar:** Karis ("Real Shading in UE4," SIGGRAPH 2013) introduced the split-sum approximation that made full IBL affordable in real time; it is now standard across UE, Unity, and Godot. Most of a material's cross-lighting "rightness" comes from this environment response, not from direct lights.
- **Test for:** PBR scenes have a defined environment/IBL source (skybox, reflection probes, or both); reflective and rough materials both read correctly against it; reflection probes are placed to cover the playable space (no obviously wrong reflections in reflective surfaces).
- **Failure mode — Missing/!inconsistent IBL:** metallic surfaces that look flat or black because there is no environment to reflect; or reflection probes so sparse that reflective materials show stale or wrong surroundings.

**→ Procedural / headless implication.** PBR is the *more* generator-safe choice precisely because energy conservation bounds the output: a generator can vary base color / roughness / metallic within vetted ranges and the result stays plausible. NPR is generator-*hostile* unless the stylization is itself a fixed parametric contract (band count, ramp, outline) — otherwise it needs a human art pass per asset. Flag NPR generation as requiring a hand-authored shading spec (→ `shaders-and-vfx`).

---

### Sub-domain C — Lighting & Shadows

#### C.1 Choose the lighting architecture by light count, material variety, and MSAA/transparency needs

**Rule:** Select forward, deferred, or forward+/clustered up front, because it constrains everything downstream. Forward: few lights, many materials, MSAA + transparency free. Deferred: many lights, uniform materials, MSAA + transparency hard. Forward+/clustered: many lights *and* MSAA/transparency, at the cost of a light-culling pre-pass and more complexity.

- **Exemplar:** Olsson, Billeter & Assarsson, "Clustered Deferred and Forward Shading" (HPG 2012) measured clustered forward at ~161 FPS vs tiled deferred ~53 FPS and tiled forward ~52 FPS at 8×MSAA 720p (GTX 680) — clustered forward keeps MSAA cheap. VR titles favor forward for exactly this MSAA/bandwidth reason. Many AAA deferred renderers accept no-MSAA + TAA as the price of many lights.
- **Test for:** the renderer choice is justified by (a) expected simultaneous dynamic light count, (b) material diversity, and (c) whether MSAA and ordered transparency are required; the G-buffer memory cost is computed for target resolution before committing to deferred.
- **Failure mode — Deferred-then-fighting-MSAA:** picking deferred for its many-lights win, then needing crisp edges and transparency, and bolting on expensive workarounds — when clustered forward would have given all three.

**→ Procedural / headless implication.** The lighting architecture is a *fixed pipeline contract*, not a per-scene generated choice. A generator may place lights within the architecture's budget (max simultaneous lights per cluster/tile) but must never switch architectures per scene; doing so changes the look of identical content.

#### C.2 Match the GI approach to how dynamic the scene is, and budget for it

**Rule:** Place each scene on the static↔dynamic axis and pick the cheapest GI that covers it: baked lightmaps for static geometry, light probes for dynamic objects in baked scenes, voxel/SDF GI for fully dynamic lighting without RT hardware, ray-traced GI only where the fidelity is worth the cost. Prefer a hybrid: a stable baked/probe baseline + targeted dynamic detail.

- **Exemplar (baked):** Most last-gen titles bake static-world bounce into lightmaps (near-zero runtime cost) and light dynamic characters with probes. **(SDF)** Godot's SDFGI gives dynamic diffuse GI with no special hardware (signed-distance-field DDGI lineage, arXiv 2007.14394). **(RT)** UE5 Lumen's hardware path is highest-fidelity but costliest — surface-cache reflections 2.44 ms vs hit-lighting 11.54 ms on PS5 (Epic SIGGRAPH data), illustrating that "ray traced" spans a 5× cost range by itself.
- **Test for:** each scene's GI method is justified by its dynamism (does lighting/geometry move?); baked content has a documented lightmap budget; dynamic GI has a measured per-frame cost under target hardware; light-leak and noise are checked at shipping quality, not editor preview.
- **Failure mode — Dynamic GI on a static scene (or vice-versa):** paying ray-traced GI cost for a scene that never changes (bake it), or baking a scene whose lighting must change at runtime (light goes stale).

**→ Procedural / headless implication.** Baked GI is fundamentally at odds with runtime-generated geometry — you cannot bake what doesn't exist at build time. A procedural/headless world therefore biases toward **probe-based or fully-dynamic GI** (SDFGI/voxel/RT), or toward *baking generated chunks at generation time* as a build step. This is a first-order constraint: flag any "bake the lighting" recommendation as incompatible with runtime generation unless a generation-time bake pass exists.

#### C.3 Tune shadow bias inside the narrow band between acne and peter-panning

**Rule:** Apply slope-scaled depth bias (scaling bias by the surface's angle to the light, near-zero for surfaces facing the light), keep near/far planes tight for depth precision, and snap the shadow projection to texel increments to stop edge shimmer. Too little bias → shadow acne; too much → peter-panning. There is no single global bias; it is a per-project tuned band.

- **Exemplar/source:** Microsoft, "Common Techniques to Improve Shadow Depth Maps" — shadow acne = depth quantization/precision causing self-shadow speckles; peter-panning = over-large depth offset detaching the shadow; slope-scaled bias, tight projections, and texel-snapping are the standard fixes. Front-face culling during the shadow pass reduces acne but can worsen peter-panning on thin geometry.
- **Test for:** at shipping shadow resolution, a flat lit surface shows no moiré/speckle (no acne) **and** contact shadows touch the base of objects (no peter-panning); shadow edges don't shimmer when the camera translates slowly (texel-snapping working).
- **Failure modes — Shadow acne** (speckled self-shadowing from too little bias / precision) · **Peter-panning** (floating objects from too much bias) · **Edge shimmer** (projection re-fit every frame without texel-snapping).

#### C.4 Use cascaded shadow maps to fight perspective aliasing over distance

**Rule:** For large outdoor scenes with a directional (sun) light, split the view frustum into distance cascades, each with its own shadow map, so near shadows get high texel density and far shadows accept blur. Tune bias/normal-offset *per cascade*, and minimize visible cascade-transition seams.

- **Exemplar/source:** CSM is "the most popular technique for dealing with perspective aliasing" (Microsoft Learn / *RTR4*); the LearnOpenGL CSM article corroborates the near-crisp / far-blurry rationale. Standard in virtually every open-world engine's directional shadows.
- **Test for:** near-camera shadows are crisp and distant shadows present (no hard cutoff); cascade-split boundaries don't show an obvious quality "wall"; per-cascade bias is set independently.
- **Failure mode — Single-map directional shadows over large distance:** either crisp-near-but-no-far or blurry-everywhere; the giveaway that cascades are missing or mis-split.

#### C.5 Filter shadow edges (PCF) to trade hard aliasing for controlled softness

**Rule:** Soften the hard, aliased edge of a raw shadow-map comparison with percentage-closer filtering (PCF) — sample the depth comparison across a small kernel and average the results — or a contact-hardening variant (PCSS) where penumbra width grows with occluder distance. Budget the kernel size; bigger kernels cost more samples per pixel.

- **Exemplar/source:** PCF and variance shadow maps are the standard edge-softening techniques (Microsoft, "Common Techniques to Improve Shadow Depth Maps," recommends PCF or VSM to soften edges). Nearly every shipped engine offers a PCF quality setting; soft shadows are a perceptual upgrade over the stair-stepped hard edge.
- **Test for:** shadow edges are softened (no single-texel stair-stepping) at shipping quality; the PCF kernel/sample count is a budgeted, documented setting; softness is consistent across cascades.
- **Failure mode — Hard aliased shadow edges:** unfiltered shadow-map comparison produces blocky, crawling shadow borders, especially under camera motion.

**→ Procedural / headless implication.** Shadow bias and cascade splits are *global hand-tuned constants*, not per-scene generated values — a generator that retunes bias per generated region will produce inconsistent acne/peter-panning across the world. Tune once against the worst-case generated geometry (thinnest walls, steepest slopes) and freeze.

---

### Sub-domain D — Performance & Culling

#### D.1 Diagnose CPU-bound vs GPU-bound before optimizing anything

**Rule:** Profile to establish whether the frame is CPU-bound (draw-call submission, game logic, culling cost) or GPU-bound (fill rate, overdraw, shader/vertex cost) *first*, because the two demand opposite fixes. CPU-bound → fewer draw calls (batch/instance), less per-object work. GPU-bound → less per-pixel work (LOD, culling, simpler shaders, lower resolution).

- **Exemplar:** The standard optimization decision tree (generalist optimization guides; consistent across engine profiling docs): if GPU time ≫ CPU time you are GPU-bound and batching won't help; if CPU time ≫ GPU time, lowering resolution won't help. Modern low-overhead APIs (Vulkan/DX12) reduce but don't remove per-draw CPU cost.
- **Test for:** every optimization task names the measured bottleneck it targets and the before/after frame-time delta; no "optimization" ships without a profile showing it addressed the actual bound.
- **Failure mode — Optimizing the wrong side:** instancing foliage in a scene that's overdraw-bound (GPU), or adding LOD to a scene drowning in draw calls (CPU) — effort with no frame-time win.

#### D.2 Cull aggressively: frustum first, then occlusion

**Rule:** Don't submit what won't be seen. Frustum-cull everything outside the camera volume (cheap, always on); add occlusion culling where large occluders hide significant geometry (GPU hierarchical-Z or precomputed visibility). Culling reduces both CPU (draw calls) and GPU (shading) load.

- **Exemplar:** Nanite folds frustum + occlusion + small-feature culling into one GPU pass via a hierarchical Z-buffer (Karis, SIGGRAPH 2021); classic engines use CPU frustum culling + portal/PVS or GPU occlusion queries. Both confirm the frustum-then-occlusion ordering.
- **Test for:** objects outside the frustum issue no draw calls; in a scene with a large occluder (wall, hill), geometry fully behind it is measurably not shaded; occlusion culling's own cost is less than the work it saves (it can lose on open scenes with few occluders).
- **Failure mode — No/insufficient culling:** rendering the whole world every frame; or occlusion culling that costs more than it saves in an open vista (profile to confirm it pays).

#### D.3 Use LOD to bound vertex/shading cost, and hide the pop

**Rule:** Swap distant meshes for cheaper versions (fewer triangles, simpler shaders/materials) so cost scales with screen size, not world size. Manage the pop-in tradeoff with hysteresis, dithered/cross-fade transitions, or continuous LOD (virtualized geometry).

- **Exemplar:** Classic discrete LOD — a 50k-tri character at 5k mid / 500 far saves ~99% of triangle work at distance with no visible loss (optimization references). Nanite's continuous, sub-pixel-error LOD eliminates visible popping by keeping switch error below one pixel.
- **Test for:** distant objects use reduced triangle/material LODs; LOD transitions don't visibly "pop" at normal play distance/speed (dither or cross-fade present); the LOD distance thresholds are tuned to target resolution/FOV (a threshold tuned for 1080p pops at 4K).
- **Failure mode — Pop-in / wrong thresholds:** objects visibly snapping to higher detail as you approach, or LOD distances tuned for one resolution that break at another.

#### D.4 Reduce draw calls with batching and instancing; control overdraw for fill rate

**Rule:** Merge draw calls — static batching for non-moving same-material meshes, GPU instancing for many copies of one mesh (foliage, crowds, debris). Separately, control **overdraw** (shading the same pixel multiple times), the dominant fill-rate cost, especially from stacked transparency.

- **Exemplar:** Static batching combines same-material static meshes into one draw call; instancing draws thousands of grass blades in one call (engine instancing docs). Overdraw's worst case is layered transparency (alpha particles, foliage cards) stacked over the same pixels — the canonical fill-rate killer (cross-reference `shaders-and-vfx`'s "particle storm" anti-pattern).
- **Test for:** many-copy content (foliage, crowds) renders via instancing, not per-object draw calls; same-material static geometry is batched; an overdraw debug view shows transparent layers don't stack beyond a budgeted depth; opaque geometry uses early-Z / front-to-back ordering to reject hidden fragments.
- **Failure modes — Draw-call flood** (thousands of individually-submitted objects, CPU-bound) · **Overdraw blowout** (stacked alpha/foliage shading each pixel many times, GPU fill-bound).

**→ Procedural / headless implication.** Generators are prone to *both* failure modes: scattering thousands of unique objects (draw-call flood — fix by instancing identical scatter, see `procedural-geometry`'s Poisson/blue-noise placement) and layering translucent VFX without a budget (overdraw blowout). Encode draw-call and overdraw budgets as validators a generated scene must pass before commit — mirror the feedback-budget linter pattern from `game-feel-and-juice`.

---

### Sub-domain E — Post-Processing

#### E.1 Run the post chain in the mandatory HDR→tonemap→LDR order

**Rule:** Keep optical, HDR-space effects (depth of field, motion blur, bloom) *before* tonemapping; tonemap HDR→LDR once; do color grading/correction *after*, in LDR; resolve AA and composite UI last. Apply exposure as a multiply *before* tonemapping.

- **Exemplar/source:** The canonical ordering (renderingevolution.net; MJP "A Closer Look at Tone Mapping"; corroborated across engine pipelines): opaque → transparency → DoF → motion blur → bloom → tonemap → color-correct → AA resolve. Bloom must see true HDR light values to bleed correctly; grading is an artistic LUT best applied to display-range colors.
- **Test for:** bloom/DoF/motion-blur read HDR scene color (pre-tonemap); color grading is an LDR LUT applied post-tonemap; exposure is applied before the tonemap curve; reordering the chain is treated as a deliberate, documented exception (some engines bloom post-tonemap for performance — note it explicitly).
- **Failure mode — Wrong post order:** bloom after tonemapping (bright sources stop blooming naturally), grading in HDR (unpredictable), or exposure applied after the curve (loses the curve's shaping).

#### E.2 Tonemap with a filmic curve and keep exposure/tonemap a global contract

**Rule:** Map HDR to display range with a filmic S-curve (ACES, Hable/Uncharted-2, or an engine default) that shapes the toe (shadows) and shoulder (highlights) — not a naive clamp or pure Reinhard if you want filmic contrast. Pick *one* tonemapper + exposure model for the whole game and hold it constant.

- **Exemplar/source:** John Hable, "Filmic Tonemapping Operators" — the Uncharted-2 parametric curve gives independent toe/shoulder control vs Reinhard's uniform x/(1+x) compression; Narkowicz's ACES fit is the UE4 default. Reinhard (2002) desaturates/compresses uniformly; filmic curves preserve highlight contrast and avoid crushed blacks.
- **Test for:** a filmic (or deliberately chosen) tonemap operator is applied globally; bright highlights roll off (don't hard-clip to white) and shadows aren't crushed black; the same exposure/tonemap config is shared by every scene.
- **Failure mode — No/naive tonemapping:** raw HDR clamped to LDR blows highlights to flat white and crushes shadows; or per-scene tonemap/exposure changes make the same asset look like different materials between areas.

#### E.3 Choose AO and AA for their specific artifacts, not by name

**Rule (AO):** Add screen-space ambient occlusion (SSAO/HBAO/GTAO) to ground contact shadows in creases, but budget it and tune falloff to avoid dark halos. **Rule (AA):** Pick the anti-aliasing method by which artifact you can tolerate — MSAA (sharp, geometry-edge-only, expensive in deferred), FXAA (cheap, blurs), TAA (resolves shimmer/temporal aliasing but ghosts and softens), SMAA (sharper than FXAA, mild ghosting).

- **Exemplar/source (AO):** SSAO (Kajalin, Crytek, Crysis 2007) — cheap but noisy with visible halos; HBAO (NVIDIA 2008) marches the depth buffer for smoother results at higher cost; both ship in nearly every 3D game. Incorrect falloff distance causes the black-halo artifact.
- **Exemplar/source (AA):** MSAA gives the sharpest edges but "can't cover the majority of aliasing in deferred-rendered games" and is bandwidth-heavy; TAA is the de-facto default in UE4/UE5, Unity HDRP, id Tech 7 because it kills shimmer — at the cost of blur and ghosting behind fast movement (Digital Foundry; Wikipedia TAA). The TAA temporal-supersampling lineage traces to Karis, SIGGRAPH 2014.
- **Test for (AO):** AO darkens creases/contacts without ringing dark halos around silhouettes; AO cost is within budget at target resolution. **(AA):** the chosen AA matches the renderer (MSAA only realistically in forward; deferred → a temporal/post method) and the project's tolerance for blur vs shimmer vs cost is documented.
- **Failure modes — AO halos** (falloff too large → dark rings) · **TAA ghosting/blur** (smear behind moving objects, soft image — needs per-game tuning + motion vectors) · **MSAA in deferred** (covers only geometry edges, misses shading aliasing, huge bandwidth).

#### E.4 Recover budget with dynamic resolution and temporal upscaling — not by cutting content

**Rule:** When GPU-bound at the target frame rate, render at a lower internal resolution and reconstruct to display resolution with a temporal upscaler (TAAU, DLSS, FSR, XeSS, or UE5's TSR) before the final AA/UI composite. Dynamic resolution scales the internal resolution per frame to hold the frame-time budget. This is the highest-leverage, content-preserving budget lever in modern rendering — but it shares TAA's ghosting risk and needs motion vectors.

- **Exemplar/source:** Temporal upscaling is now standard in shipping engines (UE5 TSR, Unity HDRP DRS, console DRS in countless titles); the lineage is the same temporal-supersampling work behind TAA (Karis, SIGGRAPH 2014). It is the reason "4K-ish" frame rates are achievable on fixed console hardware.
- **Test for:** if GPU-bound, an internal-resolution-scale + upscaler path exists before AA/UI; motion vectors feed the upscaler; the reconstruction is checked for ghosting/disocclusion artifacts on fast motion, not just static frames.
- **Failure mode — Brute-forcing native resolution:** holding native res and instead cutting visible content/effects to fit budget, when an upscaler would have preserved both the content and the frame rate; or shipping an upscaler with no motion vectors, producing heavy smear.

**→ Procedural / headless implication.** Post-processing is the strongest case for a **fixed, hand-tuned pipeline contract**: exposure, tonemapping, AO falloff, bloom threshold, and AA must be *global constants*, never per-generated-scene parameters. If a generator varies them, identical content reads as visually incoherent across the world even though each asset is individually correct. The generator may vary *scene content*; the *imaging pipeline* is frozen. This is the post-processing analogue of `game-feel-and-juice`'s frozen feedback-budget table.

---

## 4. Recommendations

**Stage 1 — Lock the pipeline contract (before any content generation):**
1. Choose the lighting architecture (forward / deferred / clustered) from expected light count + MSAA/transparency needs; compute G-buffer cost if deferred. Freeze it.
2. Choose the PBR workflow (default metallic/roughness) and an energy-conserving BRDF; or, for NPR, author a fixed parametric stylization spec. Document the channel packing.
3. Fix the imaging pipeline: the environment/IBL source (split-sum prefiltered cubemap + BRDF LUT, reflection-probe coverage), one tonemapper (filmic/ACES) + exposure model, AO method + falloff, AA method, bloom threshold — as global constants. This is the frozen "look contract."
4. Author shadow constants: slope-scaled bias band, CSM split scheme, texel-snapping — tuned against worst-case (thinnest/steepest) geometry.

**Stage 2 — Build the render-pass graph:**
5. Specify the pass graph (depth/normal/color buffers; which passes produce/consume them) so every effect lives in a stage that can perform it; neighbor-dependent effects are explicit full-screen passes.
6. Set the GI strategy by scene dynamism; for procedural worlds, default to probe/SDF/RT GI or a generation-time bake pass (baked lightmaps are incompatible with runtime geometry).

**Stage 3 — Performance scaffolding:**
7. Stand up profiling that reports CPU-bound vs GPU-bound per frame before any optimization.
8. Enable frustum culling everywhere; add occlusion culling where occluders justify it (profile that it pays in open scenes).
9. Author LOD chains (or adopt virtualized geometry) with resolution/FOV-tuned thresholds and pop-hiding (dither/cross-fade).
10. Instance many-copy content; batch same-material static geometry; add an overdraw debug view and a transparency-depth budget.

**Stage 4 — Procedural-readiness validators:**
11. Draw-call + overdraw budget linter: reject generated scenes exceeding the budget.
12. Imaging-contract guard: assert generated scenes never override exposure/tonemap/AO/AA globals.
13. Material validator: generated materials use the chosen workflow, energy-conserving, metalness near-binary, within vetted parameter ranges.
14. Mesh validator (with `procedural-geometry`): watertight, correctly-wound, valid UVs — critical before virtualized-geometry clustering.

**Thresholds that change the plan:**
- **VR / high-resolution + MSAA required:** bias toward forward or clustered forward; the deferred G-buffer bandwidth cost is often disqualifying.
- **Many dynamic lights, uniform materials, no transparency-heavy scenes:** deferred becomes attractive; accept TAA over MSAA.
- **Mobile / low-power GPU:** tighten overdraw and bandwidth hard; prefer baked/probe GI, forward, simpler tonemap; particle/transparency overdraw budgets shrink sharply.
- **Fully runtime-procedural world:** baked GI is off the table unless you add a generation-time bake; default to dynamic GI and treat its per-frame cost as a fixed budget line.
- **NPR / stylized target:** the PBR recommendations invert — author a fixed stylization contract instead, and flag per-asset shading as needing a human art pass (→ `shaders-and-vfx`).

---

## 5. Caveats

- **Engine docs are tradeoff sources, not gospel.** Vendor docs (Unreal/Unity/Godot) and vendor talks (NVIDIA mesh shaders, Epic Lumen/Nanite) describe their own engine's choices and have an incentive to favor their features. The *principles* (energy conservation, the post order, the acne/peter-panning band) are cross-corroborated; specific cost numbers (Lumen 2.44 vs 11.54 ms, clustered-forward 161 FPS) are single-platform single-benchmark and shift with hardware/driver/scene. Treat them as illustrative ratios, not portable constants. ⚠️ on every specific number.
- **Real-time vs offline/film divergence.** Everything here is *real-time* rasterization. Offline/film path tracing solves the same lighting physics by brute-force ray integration — no shadow-map bias band, no G-buffer architecture choice, no screen-space AO approximation, because it computes visibility and GI directly. Techniques that read as "fakes" here (SSAO, shadow maps, baked GI, TAA) are absent or unnecessary offline; don't import offline assumptions (unlimited bounces, no fill-rate budget) into a real-time spec.
- **The field moves fast at the GI/geometry frontier.** Virtualized geometry (Nanite), software/hardware Lumen, and ML upscalers (DLSS/FSR/XeSS, and ML denoisers for RTGI) are evolving quickly; the forward/deferred/clustered taxonomy and the PBR/post fundamentals are stable, but "what's affordable" at the GI and AA frontier in particular is a moving target — re-verify cost claims against current hardware before committing a budget.
- **PBR parameter ranges are conventions, not laws.** "Dielectric F0 ≈ 2–5%," "metalness near-binary," and similar are strong defaults from the Disney/UE4/Frostbite lineage, but artists deliberately break them for effect; the energy-conservation invariant is the hard rule, the specific ranges are guidance.
- **NPR generation is genuinely under-served by this literature.** The PBR research is deep; principled, generator-safe *stylized* rendering is thinner and more bespoke. For an AI-authored stylized game, the stylization contract will need real playtesting/art review — flag it as a risk, not a solved problem. (→ `shaders-and-vfx`, `art-direction-and-readability`.)

---

## Relationship to sibling technical-craft skills

**This skill owns the pipeline / lighting / performance / imaging layer** — the rasterization stages, the lighting architecture and shadow/GI choices, the culling/LOD/draw-call/overdraw budget, and the post-processing chain. It does **not** own:
- **Material and VFX authoring** — the shader model, node-graph material thinking, particle/decal/screen-space effects, and stylization mechanics → `shaders-and-vfx` (this skill decides *deferred-vs-forward and the overdraw budget*; that skill authors *the materials and effects that live within it*).
- **Visual readability / art direction** — silhouette, signal color, the readability-over-fidelity argument → `art-direction-and-readability`.
- **Generated meshes/terrain geometry** — noise, marching cubes, mesh topology correctness, scatter placement → `procedural-geometry` (this skill renders what that skill generates; the mesh-validity contract is shared).

**For the procedural-implication framing**, this skill repeatedly defers to the same pattern the technique modules own: a hand-authored, frozen contract (imaging pipeline, lighting architecture, shadow constants, draw-call/overdraw budget) that a generator parameterizes within but never overrides, validated by linters before commit — see `procedural-generation` for the content-coherence version and `procgen-review` for the gating step (the "oatmeal test" applies: does parameter variation alone yield perceptually distinct scenes, or does the imaging contract flatten them into sameness?).

---

## Sources

Primary / foundational:

- Akenine-Möller, Tomas, Eric Haines, and Naty Hoffman. *Real-Time Rendering*, 4th ed. CRC Press, 2018. The standard real-time pipeline reference. https://www.realtimerendering.com/
- Burley, Brent. "Physically-Based Shading at Disney." SIGGRAPH 2012 Practical Physically Based Shading course. https://blog.selfshadow.com/publications/s2012-shading-course/ · https://disneyanimation.com/publications/physically-based-shading-at-disney/
- Karis, Brian. "Real Shading in Unreal Engine 4." SIGGRAPH 2013 Physically Based Shading course. https://blog.selfshadow.com/publications/s2013-shading-course/ · https://cdn2.unrealengine.com/Resources/files/2013SiggraphPresentationsNotes-26915738.pdf
- Lagarde, Sébastien, and Charles de Rousiers. "Moving Frostbite to PBR." SIGGRAPH 2014. https://blog.selfshadow.com/publications/s2014-shading-course/
- Karis, Brian. "Nanite: A Deep Dive." SIGGRAPH 2021 Advances in Real-Time Rendering. https://advances.realtimerendering.com/ · https://dev.epicgames.com/documentation/en-us/unreal-engine/nanite-virtualized-geometry-in-unreal-engine
- Karis, Brian. "High-Quality Temporal Supersampling." SIGGRAPH 2014 Advances in Real-Time Rendering (TAA lineage). https://advances.realtimerendering.com/s2014/
- Olsson, Ola, Markus Billeter, and Ulf Assarsson. "Clustered Deferred and Forward Shading." High Performance Graphics 2012; and "Tiled and Clustered Forward Shading," SIGGRAPH 2012 Talks. https://www.cse.chalmers.se/~uffe/tiled_shading_siggraph_2012.pdf
- Harada, Takahiro. "Forward+: Bringing Deferred Lighting to the Next Level." Eurographics 2012 short paper.
- Microsoft. "Common Techniques to Improve Shadow Depth Maps." Win32 graphics tech articles (shadow acne, peter-panning, slope-scaled bias, CSM). https://learn.microsoft.com/en-us/windows/win32/dxtecharts/common-techniques-to-improve-shadow-depth-maps
- Hable, John. "Filmic Tonemapping Operators." Filmic Worlds. https://filmicworlds.com/blog/filmic-tonemapping-operators/
- Narkowicz, Krzysztof. "ACES Filmic Tone Mapping Curve." 2016. https://knarkowicz.wordpress.com/2016/01/06/aces-filmic-tone-mapping-curve/
- Pettineo, Matt (MJP). "A Closer Look At Tone Mapping." https://therealmjp.github.io/posts/a-closer-look-at-tone-mapping/
- Reinhard, Erik, et al. "Photographic Tone Reproduction for Digital Images." SIGGRAPH 2002.
- Motomura, Junya C. "GuiltyGearXrd's Art Style: The X Factor Between 2D and 3D." GDC 2015. https://www.gdcvault.com/play/1022031/GuiltyGearXrd-s-Art-Style-The · https://www.ggxrd.com/Motomura_Junya_GuiltyGearXrd.pdf
- Kajalin, Vladimir (Crytek). SSAO, first shipped in Crysis (2007). https://en.wikipedia.org/wiki/Screen_space_ambient_occlusion
- Bavoil, Louis, et al. (NVIDIA). "Horizon-Based Ambient Occlusion." 2008.
- Majercik, Zander, et al. "Dynamic Diffuse Global Illumination with Ray-Traced Irradiance Fields." JCGT / I3D 2019 (DDGI). https://www.jcgt.org/published/0008/02/01/
- "Signed Distance Fields Dynamic Diffuse Global Illumination" (SDFGI lineage). arXiv 2007.14394. https://arxiv.org/pdf/2007.14394

Secondary / corroborating:

- NVIDIA. "Introduction to Turing Mesh Shaders." https://developer.nvidia.com/blog/introduction-turing-mesh-shaders/
- LearnOpenGL — Shadow Mapping & Cascaded Shadow Maps. https://learnopengl.com/Advanced-Lighting/Shadows/Shadow-Mapping · https://learnopengl.com/Guest-Articles/2021/CSM
- "Order of post-process effects." Rendering Evolution. https://www.renderingevolution.net/?p=103
- Engine documentation as tradeoff sources (not code): Unreal Engine (Lumen, Nanite, post process), Unity URP/HDRP, Godot (SDFGI, rendering) — used for engine-specific tradeoffs only.
