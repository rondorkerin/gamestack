# Animation Systems — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for a character/object motion system. Run against a rig spec, a blend-architecture pass, or a batch of generated characters. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** rig & skinning → blend architecture → procedural/physics layer → pipeline & cost → timing. And: pick every technique per-case (per joint, chain, clip, motion space), never one global setting.

---

## A · Skeletal fundamentals (rig, skinning, IK)

**Do**
- [ ] Default to **LBS**; use **DQS** on hard-twisting joints (wrist/forearm/shoulder/neck); reserve **Centers of Rotation** for hero characters.
- [ ] Drive authored motion with **FK**; layer **IK** only where the body touches a runtime-variable target (ground, ledge, look, aim).
- [ ] Use an **analytical two-bone** solver for limbs; **FABRIK/CCD** (capped iterations) for long chains; **look-at** for aim/head.
- [ ] **Retarget** one clip library onto every character sharing a reference skeleton (proportion-compensated, contacts preserved) instead of authoring per-character.

**Don't**
- [ ] Don't pick one skinning method globally without checking the worst-twisting joint.
- [ ] Don't let an IK target pull a joint past the clip's intent, or snap IK weight 0→1.
- [ ] Don't run an iterative solver where a two-bone solve suffices.
- [ ] Don't author bespoke clips per character (defeats reuse, balloons memory) or retarget across mismatched hierarchies/naming.

**Test for** — Twist the worst joint to ±180°: any silhouette collapse (LBS pinch / DQS bulge)? Disable IK: does base FK still read correctly? Does every 2-segment limb use the analytical solver? Apply the same set to the shortest and tallest characters: feet contact, hands reach, no hyperextension?

---

## B · Blending & state architecture

**Do**
- [ ] Put **discrete modes** (Idle/Locomotion/Combat/Climb/Dead) in a **state machine** with explicit transitions + blend durations.
- [ ] Put **continuous variation** (speed, direction) in **1D/2D blend trees** on orthogonal, normalized axes.
- [ ] **Phase-sync** every clip in a blend set (footfall timing); compose regions with **additive layers + bone masks** (upper/lower split).

**Don't**
- [ ] Don't represent a continuous axis as a fan of discrete states (state explosion / boundary pops).
- [ ] Don't blend across a true mode change (alive→dead).
- [ ] Don't let two unmasked layers write the same bone (jitter), or hard-cut a mask mid-spine (seam).

**Test for** — Sweep the input axis: continuous, no pop? Drive the blend to interior points (diagonal half-speed): feet still contact, no moonwalking? Play every lower-body state under each upper-body overlay: clean mask boundary?

---

## C · Procedural & physics-driven motion

**Do**
- [ ] Use **active ragdoll** (physics constrained to track the animation target) for gameplay reactions; passive ragdoll only for death.
- [ ] Add **foot-placement IK** (raycast + two-bone solve + normal-orient + pelvis adjust) before authoring terrain-specific clips.
- [ ] Add **spring-bone** secondary motion (Verlet, damped, budgeted) for hair/cloth/tails.
- [ ] Reach for **motion matching** only when the motion space is too large/responsive for an authored graph — with a curated database + acceleration structure + memory budget.

**Don't**
- [ ] Don't run physics open-loop on non-physical gameplay poses (blowup), or over-stiffen tracking (invisible reaction).
- [ ] Don't apply foot IK without pelvis adjustment (over-extension) or normal-orientation (heel float).
- [ ] Don't ship under-damped springs (diverge at low FPS) or uncapped spring-bone counts (bone-budget blowout).
- [ ] Don't grow a motion-matching database past the memory budget or brute-force the search with no acceleration structure.

**Test for** — Impulse mid-animation: believable deform *and* recovery to the FK target? Walk a slope/step/gap: both feet contact, foot orients, no knee pop? Stop abruptly: secondary elements overshoot-and-settle? Motion matching: input→matched-pose latency within the feel budget, memory within platform?

---

## D · Pipeline & cost model

**Do**
- [ ] Budget **bone count** against skinning cost (not just memory); add bones only where deformation demands; share skeletons.
- [ ] Compress with **quantization + curve fitting**, using **adaptive error margins** (tighter for longer descendant chains).
- [ ] Tag every clip **root-motion** or **in-place**; reconcile with **foot-lock IK + timescale**.

**Don't**
- [ ] Don't add decorative bones (multiplies every clip's memory + every frame's skinning across every instance).
- [ ] Don't use a single global compression tolerance (accumulates into foot-sliding at the leaf joints).
- [ ] Don't let root motion fight a code-driven position (rubber-banding), or run in-place at the wrong speed (skating).

**Test for** — Bone count within budget; profile pose+skinning at max on-screen characters. Compare compressed vs source on the *end-effectors*: foot-slide/contact-drift under threshold? Each locomotion clip: stance-phase world-space foot velocity ≈ 0?

---

## E · Readability & timing (delivers `game-feel-and-juice`'s rules)

**Do**
- [ ] Decompose each action into a **`(anticipation, active, recovery)`** triplet with phase boundaries as exposed data.
- [ ] Keep **one timing signature per action type** across all characters that share it (Principle 9).
- [ ] Decide **animation-priority vs cancellable per action** (a deliberate tag); expose cancel windows on the recovery phase; pair priority moves with input buffering.
- [ ] Let follow-through VFX/secondary motion **outlast the recovery phase** so control returns before the visual fully settles.

**Don't**
- [ ] Don't randomize per-instance timing (reads as buggy; blocks mastery).
- [ ] Don't ship an *unchosen* commitment default — long non-cancellable recovery with no buffering/dodge-out is the "animation-locked" trap; everything-cancellable erases weight.
- [ ] Don't re-decide the timing *values* here — those are `game-feel-and-juice`'s call (enemy wind-up length, player responsiveness ceiling).

**Test for** — Does each action expose anticipation/active/recovery boundaries? Do all instances of an action type share one signature? Is each action's commitment model a deliberate tag, and are cancel windows data on the recovery phase?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Rig + skinning contract** (frozen hierarchy, bind pose, per-joint skinning method, IK-chain definitions) → **rig/skin validator** (reject off-contract bone counts/weights; reject worst-joint silhouette collapse beyond threshold).
- [ ] **Clip library + blend-space axes** (phase-tagged clips, additive overlays with reference poses) → **blend-set validator** (reject mixed footfall-phase tags; reject overlays missing a reference pose).
- [ ] **Bone budget + compression tolerances + motion-type tag** (root-motion vs in-place per clip) → **cost validator** (reject over-budget rigs, uncompressed/mis-tagged clips, stance-phase foot velocity over the sliding threshold).
- [ ] **Action library** of `(anticipation, active, recovery)` triplets with one timing signature + cancel/commitment tags per action type → **animation-state validator** (shared with `game-feel-and-juice`; no generator-computed timings).

> The generator **selects and warps** within these contracts (retarget, blend, IK-correct, motion-match) — it never invents bone counts, timing signatures, or uncapped physics. For the *design rules* these timings must satisfy (anticipation inversion, follow-through, responsiveness ceiling) apply `game-feel-and-juice`; for combat telegraphing and hit-stop apply `combat-design`; gate every generated batch through `procgen-review`.
</content>
