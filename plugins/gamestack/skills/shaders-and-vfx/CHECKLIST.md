# Shaders & VFX — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for shader/material authoring and VFX. Run against a material-authoring pass, a VFX implementation, a stylization pass, or a batch of generated content. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** lock the shader-stage contract and master materials → build the parametric material/VFX palette → stylize → validate budgets. Don't let a generator author shader code or uncapped emitters.

---

## A · The shader model (vary parameters, not plumbing)

**Do**
- [ ] Place each computation in the stage whose **write-capability** it needs (vertex transform · fragment one-pixel · compute arbitrary-buffer); **calculate in the vertex shader, use in the fragment shader**.
- [ ] Feed per-pixel logic through the cheapest channel: **uniform** (constant per draw), **varying** (interpolated per-vertex), **texture** (random-access).
- [ ] Keep the generator's authoring surface to **uniforms, textures, and parameters** into a small set of hand-authored master materials.

**Don't**
- [ ] Don't recompute constant or per-vertex math per fragment.
- [ ] Don't over-stuff interpolators with data that could be a uniform.
- [ ] Don't let a generator synthesize new shader stage code (re-opens variant explosion).

**Test for** — Does every per-pixel computation genuinely vary per pixel? Are varying inputs minimized to only what needs interpolation?

---

## B · Material authoring (parametric, generator-safe)

**Do**
- [ ] Author materials as a **node graph** of composable, typed operations; expose a documented parameter set with **min/max ranges**.
- [ ] Use **procedural texturing** where it parameterizes or where there are no UVs (**triplanar** for runtime geometry; **vertex-color/splat masks** for terrain blends; **noise/SDF** for detail and free instancing).
- [ ] Use **baked textures** for unique, art-directed, hand-detailed hero surfaces.
- [ ] Seed procedural parameters **deterministically** (a seed reproduces the surface).
- [ ] When displacing an SDF with fBm, **smax-confine + smin-merge** — never arithmetic addition.

**Don't**
- [ ] Don't ship a parameter that breaks the material (NaN, inverted, invisible) at any in-range value.
- [ ] Don't apply UV-based materials to UV-less runtime geometry (voxel/marching-cubes) — stretched garbage.
- [ ] Don't use one noise basis at one scale everywhere (procedural sameness — the oatmeal problem).
- [ ] Don't arithmetically add two SDFs (breaks the unit-gradient field; raymarchers fail).

**Test for** — Does no in-range parameter value produce a broken material? Do runtime-generated meshes use a UV-free path? Does SDF displacement preserve approximate unit gradient with no "flyover" fragments?

---

## C · The VFX toolkit (each primitive has a cost model)

**Do**
- [ ] Choose **CPU vs GPU particles** by the count×coupling quadrant (GPU = high count / visual-only; CPU = low count / gameplay-coupled); declare a **max count + emitter cap** per system.
- [ ] Use **ribbons/trails** or **velocity-stretched billboards** for continuous fast motion (slashes, projectiles).
- [ ] Use **deferred/screen-space decals** for dynamic surface marks; clamp by surface-normal angle.
- [ ] Share **one scene-color capture per frame** across all distortion/refraction.
- [ ] Drive **dissolve / hit-flash / rim** each by a single scalar uniform; take flash/rim colors from the **signal palette** (→ `art-direction-and-readability`).

**Don't**
- [ ] Don't run thousands of CPU particles, or use GPU particles for an effect that must trigger gameplay.
- [ ] Don't render fast effects as static sprites (they strobe into a dotted line).
- [ ] Don't grab the framebuffer once per distortion effect (bandwidth multiplies).
- [ ] Don't apply Fresnel rim to everything until nothing reads as special.

**Test for** — Does each VFX primitive declare its count/emitter budget? Do fast effects use stretched billboards/ribbons? Does all distortion in a frame read from a single shared scene-color capture? Is each screen-space effect driven by one uniform a generator can set?

> Boundary: this section owns *how each channel is built and what it costs*. *When/why/how-much* it fires — and the inverted-U budget ceiling — is `game-feel-and-juice`.

---

## D · Stylization (technique that realizes the art-direction language)

**Do**
- [ ] For cel/toon: **band the lighting** into a few discrete steps and treat **normals as an authorable input** (hand-edited / smoothed), not the geometric normal.
- [ ] Pick the **outline family by topology**: inverted hull only on smooth/convex meshes; post-process edge detection (depth+normal) where geometry-clip-free outlines are needed.
- [ ] Judge **readability at gameplay camera distance first**; confirm the style hits its frame-rate target on min-spec.

**Don't**
- [ ] Don't apply a toon ramp to raw geometric normals of noisy procedural meshes (flickering shadow lines).
- [ ] Don't put inverted-hull outlines on hard-edged/concave generated meshes (holes/spikes).
- [ ] Don't optimize for the close-up beauty shot at the expense of gameplay-distance legibility.

**Test for** — Is the lighting ramp a few discrete bands? Are normals an authorable input the generator is handed? Does the chosen outline method match mesh topology? At gameplay distance, are enemies/hazards/interactables distinguishable by silhouette + signal color within a glance?

---

## E · Performance & failure modes (machine-checkable budgets)

**Do**
- [ ] **Measure and bound** total shader-variant / PSO count; **precache** hot PSOs at load/spawn; cut rare permutations.
- [ ] Keep **overdraw** under a per-scene ceiling at peak effect load; use the simplest possible particle shaders; prefer fewer large particles; render dense systems off-screen at half-res.
- [ ] **Cap concurrent on-screen VFX** so gameplay-critical signals stay readable under maximum load.

**Don't**
- [ ] Don't ship an über-shader whose keyword/toggle product is an unshippable variant count.
- [ ] Don't allow new PSO compilation during steady-state play (compilation stutter).
- [ ] Don't put per-pixel lighting on smoke/transparent particle shaders.
- [ ] Don't measure VFX by quantity — the particle storm tanks FPS *and* hides the gameplay state.

**Test for** — Is variant/PSO count measured and under cap with a precache manifest? Does an overdraw debug view stay under the per-scene ceiling at peak load? At maximum simultaneous ability/combat load, are enemy tells, hazard zones, and player position still identifiable?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Master materials** (small set, parameterized) → generator varies uniforms/textures/parameters only, never authors shader code.
- [ ] **Material-parameter validator** — every generated material's parameters in range; runtime geometry uses a UV-free path; SDF displacement preserves the field (B).
- [ ] **VFX recipe palette** (each effect a bounded template — CPU/GPU fixed, count/size ranges, color from the signal palette) → generator selects and parameterizes, never invents emitters/shaders.
- [ ] **Variant/PSO validator** — total count under cap; precache manifest present; no steady-state compile hitches (E.1).
- [ ] **Overdraw / fill-rate validator** — overdraw under the per-scene ceiling at simulated peak VFX load (E.2).
- [ ] **Concurrent-VFX validator** — emitter count + screen-coverage caps; gameplay signals readable under max load (E.3).

> For *when/why/how-much* each feedback channel fires and the inverted-U ceiling, apply `game-feel-and-juice` (this skill is its rendering substrate). For the rendering pipeline / lighting / post-processing order these materials live inside, see `3d-graphics-and-rendering`. For the signal-color and silhouette language stylization realizes, see `art-direction-and-readability`. Gate every generated batch through `procgen-review`.
</content>
