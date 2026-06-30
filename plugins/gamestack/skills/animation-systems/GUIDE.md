# Animation Systems — Guide

The architecture of a character/object motion system: the rig and skinning underneath, the blend/state-machine/IK layer that composes authored motion, the procedural and physics layer that fits it to the world, the compression/cost model that ships it, and the timing decomposition that delivers it. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from the SIGGRAPH 2014 skinning course (Kavan et al.), Disney's dual-quaternion and Optimized-Centers-of-Rotation work (Le & Hodgins 2016), Simon Clavet's GDC 2016 Motion Matching talk (*For Honor*) and Naughty Dog's *The Last of Us Part II* (GDC 2021), Holden et al.'s Learned Motion Matching and DReCon (Ubisoft La Forge), David Rosen's procedural-animation talk (*Overgrowth*, GDC 2014), Rune Skovbo Johansen's semi-procedural locomotion (GDC 2009), Riot's animation-compression writeup, and Richard Williams' *The Animator's Survival Kit* for the timing craft. Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round3-technical/T3`).

> **The central law:** *every technique is a cost/quality dial picked per-case, and generation composes a finite hand-authored library — it never invents the contract.* Skinning, IK solver, root-motion choice, and authored-graph-vs-motion-matching each have a cheap default, a costlier upgrade, and a named artifact when misapplied; the rig, clips, blend axes, IK/spring params, and timing signatures are frozen contracts a generator selects and warps within.

> This skill is the **implementation layer under `game-feel-and-juice`'s animation-timing principles.** The design rules — the player-vs-enemy anticipation inversion, follow-through as weight-without-recovery, the 12 principles, the responsiveness ceiling — live there; this skill owns the *system* that delivers them. It cites *up*; it does not re-decide them.

---

# Sub-domain A — Skeletal Animation Fundamentals

> A skeleton is a tree of joints (each a local transform relative to its parent); a bind pose records the rest skeleton; skinning maps the deformed skeleton onto the mesh via per-vertex bone weights. Forward kinematics plays authored joint rotations; inverse kinematics solves joint rotations from a desired end-effector position.

## A.1 Choose the skinning method per joint, on the volume-vs-cost ladder

Default to **linear blend skinning (LBS)**; escalate to **dual-quaternion skinning (DQS)** on hard-twisting joints (wrists, forearms, shoulders, necks); reserve **Optimized Centers of Rotation (CoR)** for hero characters. LBS averages bone *matrices*, so at a 180° twist the surface collapses toward the bone axis — the "candy-wrapper." ✅ (SIGGRAPH 2014 skinning course; Disney Enhanced DQS, used on *Frozen*; Le & Hodgins 2016.) DQS fixes the collapse but adds a bulging-joint/distorted-normal artifact; CoR fixes both at higher precompute. None reproduce muscle bulge — that needs corrective blend shapes.

- **Test for:** twist the worst joint (forearm/wrist) to ±180° in bind pose; LBS pinches toward the axis, DQS bulges at the bend. Confirm no visible collapse at the *worst* joint, not the average.
- **Failure mode — global skinning choice:** DQS-everywhere pays its cost + bulge on joints that never twist; LBS-everywhere ships the candy-wrapper on every wrist.

## A.2 FK for authored poses, IK for world contact

Let FK drive the authored motion; layer IK only where the body must touch a runtime-variable target — ground, ledge, look target, aim direction. IK is a *correction* on top of FK, not a replacement. ✅ (foot placement, hand-on-ledge, look-at constraints, 2D aim offsets — MoCap Online; engine IK docs.)

- **Test for:** disable IK → base FK still reads correctly on flat ground / neutral aim; enable IK → feet plant on slopes, reticle aligns with the muzzle.
- **Failure mode — IK fighting FK:** a target past the clip's intent pops or hyperextends; a 0→1 weight snap jerks. Blend IK over several frames; clamp joint limits.

## A.3 Match the IK solver to the chain length

Analytical **two-bone** for limbs (closed-form, no iteration, effectively free — dozens per frame); iterative **FABRIK/CCD** for long chains (spines, tails, tentacles), cost scaling with chain × iterations; **look-at** for single-joint aim/head tracking. ✅ (MoCap Online; Godot 4 IK modifier stack; CCD/FABRIK implementations.)

- **Test for:** every 2-segment limb uses the analytical solver; only genuinely long chains use an iterative one, with a capped iteration count.
- **Failure mode — solver mismatch:** iterative-on-every-limb burns CPU for no gain; too-few iterations on a long chain leaves the end-effector short (visible reach failure).

## A.4 Retarget one clip library across many skeletons

Author animation once on a reference skeleton and **retarget** it onto every character sharing a compatible hierarchy — compensating for limb proportions, preserving foot/hand contacts — so a finite clip library covers an unbounded roster. Standardize one skeleton/naming convention up front. ✅ (bone-name mapping + proportion scaling + contact preservation; Unity Humanoid Avatar, Unreal IK Retargeter, MotionBuilder, Maya HumanIK.)

- **Test for:** apply the same set to the shortest and tallest characters → feet contact, hands reach props, no hyperextension; all rigs share the reference hierarchy/naming.
- **Failure mode — retarget breakage:** mismatched hierarchies/naming twist limbs or break foot contact; per-character bespoke clips defeat the reuse model and balloon memory.

**→ Procedural / headless implication.** Retargeting *is* the combinatorial-reuse mechanism: the rig + a single clip library are the frozen contract, and a generator dresses an unbounded roster by retargeting onto skeletons sharing the bind hierarchy — never inventing bone counts/weights per-asset (skinning artifacts ship silently to a headless generator). Expose reference skeleton + skinning method + IK-chain definitions as per-rig data selected from a vetted set; validate by twisting the worst joint headlessly (reject silhouette collapse) and validate retargets on proportion extremes.

---

# Sub-domain B — Animation Blending & State Architecture

> State machines own *discrete* modes; blend trees own *continuous* variation within a mode; additive layers + bone masks compose *independent* regions. They are complementary, not competing — production humanoids use all three.

## B.1 Discrete modes in a state machine, continuous variation in a blend tree

Model mutually-exclusive behaviors (Idle / Locomotion / Combat / Climb / Dead) as states with explicit transitions; model smooth interpolation within a behavior (idle→walk→run by speed) as a blend tree. ✅ (1D blend on speed; 2D blend on speed×direction for strafing without turning — Unity/Unreal/Godot docs; MoCap Online.)

- **Test for:** sweep the input axis → continuous output, no pop; trigger each transition → fires on its condition with a defined blend duration.
- **Failure mode — state explosion / blend misuse:** every speed as its own state = unmaintainable graph + boundary popping; blending across a true mode change (alive→dead) = nonsensical half-state.

## B.2 Build 2D blend spaces on orthogonal, normalized, phase-synced clips

Choose independent normalized axes (fwd/back × left/right velocity, or speed × turn-rate); place clips at the extremes and zero; **phase-sync** the footfalls so feet line up when blended. ✅ (8-way directional sets blended by the movement vector.)

- **Test for:** drive the blend to interior points (diagonal half-speed) → feet still contact in a plausible cadence; the four cardinal extremes match their authored clips.
- **Failure mode — unsynchronized blend:** clips at different footfall phases blend into "moonwalking" / hovering; non-orthogonal axes make some inputs unreachable or double-counted.

## B.3 Layer independent regions with additive animation + bone masks

Express overlays that must combine with *any* base (aim offset, lean, hit-react, breathing, facial) as **additive** layers — a delta from a reference pose; isolate regions with per-bone masks so an upper-body state machine runs in parallel with lower-body locomotion. ✅ (Layered Blend Per Bone / Avatar Masks; the upper/lower split is the foundational humanoid pattern.)

- **Test for:** play every lower-body state while firing each upper-body overlay → overlay reads on top of all; the mask boundary (spine) shows no kink.
- **Failure mode — double-driven / torn bones:** two unmasked layers writing one bone fight and jitter; a hard mask mid-spine = a "broken back" seam.

**→ Procedural / headless implication.** Blend architecture is generator-friendly *because* it is combinatorial: a finite clip set × blend axes × additive overlays covers a large motion space with no new clips. Expose the axes + additive set as data; require every clip in a blend set to carry a footfall-phase tag. The validator rejects mixed-phase blend sets and additive overlays missing a reference pose.

---

# Sub-domain C — Procedural & Physics-Driven Motion

> IK, spring bones, active ragdoll, and motion matching all generate or correct motion at runtime — the leverage that makes many characters move from a finite library without bespoke per-variant clips.

## C.1 Active ragdoll, not passive, when reactions must stay characterful

Passive ragdoll (hand the skeleton to physics) is fine for death; for *reactions* (stagger, push, partial hit), drive a simulated body toward the authored animation target (active ragdoll / powered constraints) so it reacts to force while still performing intended motion. Constrain physics to track animation — never open-loop on gameplay poses. ✅ (Rosen/Wolfire GDC 2014, *Overgrowth*; Ubisoft DReCon SIGGRAPH Asia 2019 = RL controller tracking a motion-matching target, fully simulated, low runtime cost; Naughty Dog *Uncharted 4* — naive physics-on-animation "was pretty terrible" until physics was driven *from* animation predictably.)

- **Test for:** impulse mid-animation → believable deform *and* recovery to the authored pose; cut force → converges back onto the FK target with no residual jitter.
- **Failure mode — physics blowup / dead reaction:** open-loop physics on non-physical poses explodes or goes limp; over-stiff tracking makes the reaction invisible.

## C.2 Plant feet with runtime IK before authoring more clips

Raycast beneath each foot, move it to the hit point, orient to the surface normal, adjust pelvis height so no leg over-extends — adapting flat-ground cycles to arbitrary terrain at near-zero authoring cost. ✅ (Rune Johansen's Locomotion System, GDC 2009 — velocity-map blend + minimal leg-bone adjustment so "feet step correctly on the ground" across slopes/steps; "semi-procedural" = author the cycle, IK corrects minimally.)

- **Test for:** walk a slope, a step, a gap edge → both feet contact the actual surface, the foot orients to the slope, no knee hyperextends/pops. Toggle IK off to confirm what it's correcting.
- **Failure mode — floating/clipping feet & pelvis pop:** foot IK ignoring pelvis height over-extends on a step; no normal-orientation leaves the foot flat on a slope (heel floats); un-blended IK snaps.

## C.3 Secondary motion with spring bones — stable and budgeted

Drive hair, cloth, tails, pouches, soft tissue with lightweight spring/jiggle bones (base animation moves the root; a spring/damper drags the rest), integrated stably (**Verlet**) so it doesn't diverge at low FPS. Budget the bone count — secondary motion looks free, isn't. ✅ (standard spring-bone practice; Verlet preferred for stability/perf.)

- **Test for:** stop abruptly → elements overshoot and settle (follow-through), don't snap; drop FPS → simulation stays bounded; spring-bone budget stays within the skinning ceiling (D.1).
- **Failure mode — explosion or dead weight:** under-damped springs at low FPS diverge and fling the mesh; over-damped read as stiff; uncapped counts blow the bone budget.

## C.4 Motion matching when an authored graph can't cover the motion space

Every few frames, search a curated mocap database for the frame best matching current pose + desired trajectory, jump there, then warp procedurally (timescale, foot-lock, slope) to hit exact gameplay constraints. ✅ (Clavet GDC 2016, *For Honor* — "look at all mocap and jump at the best place," switching multiple times/sec; Naughty Dog TLOU2 GDC 2021 — locomotion, traversal, melee, cover.) **It trades hand-authoring for memory:** vanilla MM exceeds 0.5 GB and scales linearly with data; Learned Motion Matching (Holden et al., SIGGRAPH 2020) compresses a 590 MB controller to ~8.5 MB (~70×). ✅

- **Test for:** a curated, marked-up database exists; an acceleration structure (KD-tree/LOD) bounds per-frame search cost; input→matched-pose latency meets the feel budget; memory fits the platform.
- **Failure mode — data starvation & memory blowout:** too small/uncurated a database snaps or drifts; the database outgrows the budget; brute-force search with no acceleration tanks the frame. Mitigate with learned/compressed variants or a hybrid state machine.

**→ Procedural / headless implication.** This is the generator's best friend: a generated creature moves by retargeting a base locomotion set + foot IK + spring-bone tails, no hand-keyed clips per variant; motion matching draws many characters from one corpus. But the corpus, IK params, spring budgets, and active-ragdoll constraints are hand-authored contracts validated against stability/budget thresholds — the generator varies inputs, never authors physics constants.

---

# Sub-domain D — The Pipeline & Cost Model

> Bone count drives both memory and per-frame skinning cost; compression is two orthogonal techniques with a shared foot-sliding failure; root motion and in-place fail in opposite directions.

## D.1 Budget bone count against skinning cost, not just memory

Each bone is a transform track per frame (memory) *and* a transform applied during mesh update (the dominant runtime skinning cost). Add bones only where deformation demands; share skeletons across characters. ✅ (doubling bones ≈ doubles memory and often needs more keyframes; skinning cost scales with bone/vertex count, not keyframe count.)

- **Test for:** each rig's bone count within the platform budget, bones where deformation needs them (no decorative bloat); profile pose+skinning time at max simultaneous on-screen characters.
- **Failure mode — bone bloat:** a high count "for flexibility" multiplies every clip's memory + every frame's skinning across every instance; crowds become CPU/GPU-bound on the skinning pass.

## D.2 Compress with quantization + curve fitting, guarded by adaptive error margins

Quantize per-channel bit depth **and** curve-fit to drop redundant keyframes within an error tolerance — but make the tolerance *adaptive*: tighter for joints with longer descendant chains, because error compounds down the chain into foot-sliding at the leaf. ✅ (Riot: rotations to 48 bits/quaternion [15×3 + 2 to flag the omitted component], 0.375 ratio, 0.000043 precision; Catmull-Rom curve fit, 661 frames → 90; ~25% of original; tighten threshold for longer descendant chains. Quantization is load-time cheap; curve fitting is a heavy preprocess.)

- **Test for:** compare compressed vs source on the *end-effectors* (hands, feet), not the root; foot-slide/contact drift under a perceptual threshold; tolerance scales with descendant-chain length.
- **Failure mode — uniform-tolerance foot-slide:** a global tolerance fine at the hip accumulates into visible sliding at toes/fingertips; over-aggressive key removal flattens snappy motion into mush.

## D.3 Root motion vs in-place per clip, reconciled with foot-lock

**Root motion** for committed displacement-exact moves (vaults, mantles, takedowns, turn-in-place) — authentic weight, but cedes velocity control and desyncs if code overrides it. **In-place + code velocity** for free locomotion — full control, but skates feet when code speed ≠ clip speed. Reconcile with foot-lock IK + timescale. ✅ (engine-community consensus; Clavet's warps — timescale, foot-locking, slope — are the production reconciliation.)

- **Test for:** each locomotion clip → stance-phase world-space foot velocity ≈ 0 (no sliding); each root-motion set-piece → end position matches the gameplay target, no rubber-banding.
- **Failure mode — desync both ways:** root motion fighting code position rubber-bands; in-place at the wrong speed skates; a clip authored as one but consumed as the other teleports or slides.

**→ Procedural / headless implication.** The cost model is the generator's guardrail. Bone budgets, compression tolerances, and the root-motion/in-place tag are per-clip *metadata* the generator respects, not recomputes. Reject over-budget rigs, uncompressed/mis-tagged clips, and clips whose stance-phase foot velocity exceeds the sliding threshold.

---

# Sub-domain E — Readability & Timing

> This skill owns the *system* that delivers timing; `game-feel-and-juice` owns the *design rule*. Do not re-decide the rule here.

> **Division of labor:** that anticipation reads as a fair warning on enemies but as input lag on the player avatar, and that follow-through sells weight without extending recovery, is owned by `game-feel-and-juice` (its 12-principles section, grounded in Thomas & Johnston and Richard Williams' *Animator's Survival Kit* timing/spacing/weight craft). Here we own the architecture that produces it: the clip decomposition, the cancel windows, the blend that returns control fast while the visual follows through.

## E.1 Author every action as a timed `(anticipation, active, recovery)` triplet

Decompose each action into three explicitly-tagged phases with durations as exposed data, so the same action shares one timing signature everywhere and gameplay can key off phase boundaries (hit-active windows, cancel points). ✅ (Williams grounds the timing/weight; game systems realize it as phase-tagged clips gameplay queries.)

- **Test for:** each action exposes anticipation/active/recovery boundaries as data; the same action type uses *one* timing signature across all characters sharing it (Principle 9 — consistency enables mastery); follow-through VFX/secondary motion outlasts the recovery phase (control returns before the visual fully settles).
- **Failure mode — inconsistent/merged phases:** randomized per-instance timing reads as buggy and blocks mastery; an action with no distinct anticipation can't be telegraphed (enemy) or feels laggy (player) — but *which* is the bug is a `game-feel` design call.

## E.2 Decide animation-priority vs cancellable per action, and tune cancel windows

Decide, per action, whether it is **animation-priority** (plays to completion — commitment, weight, risk) or **cancellable** (interruptible into another action — fluid, responsive). Expose cancel windows as data on the recovery phase; pair animation-priority with input buffering so the *next* input still feels honored. ✅ (the action-combat split between "animation priority" and "cancellable offense"; canceling = interrupting an in-progress animation with another.)

- **Test for:** each action's commitment model is a deliberate tag, not an accident of clip length; cancel windows are data on the recovery phase; input buffering carries a press during a non-cancellable action to its first legal frame (→ `game-feel-and-juice` B.1).
- **Failure mode — the "animation-locked" trap:** long non-cancellable recovery + no buffering + no dodge-out = unresponsive, unfair ("committed with no way to bail"); the opposite extreme (everything cancellable) erases weight and risk. Both are failures of an *unchosen* default; the right point is a per-game design decision.

**→ Procedural / headless implication.** Timing + cancel data are a hand-authored contract the generator selects from, never invents. Author a fixed library of phase-tagged action clips, one timing signature per action type, explicit cancel-window/commitment tags; a generator assigns library entries but must not compute new timings. Validate that every generated action references a library entry and all instances of an action type share its signature — the shared `animation-state validator`.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Candy-wrapper | Mesh pinches to a point on twist | DQS on hard-twisting joints (A.1) |
| Global skinning choice | Bulge everywhere, or pinch everywhere | Pick method per joint group (A.1) |
| IK fighting FK | Popping / hyperextension / jerk | Blend IK in/out; clamp joint limits (A.2) |
| Solver mismatch | Wasted CPU, or end-effector falls short | Two-bone for limbs, FABRIK/CCD for long chains (A.3) |
| Retarget breakage | Twisted limbs, broken foot contact across characters | One reference skeleton; validate on proportion extremes (A.4) |
| State explosion | Unmaintainable graph, boundary pops | Discrete = state, continuous = blend tree (B.1) |
| Moonwalking blend | Feet slide/hover at interior blend points | Phase-sync footfalls; orthogonal axes (B.2) |
| Torn / double-driven bones | Jitter or a "broken back" seam | Additive layers + bone masks (B.3) |
| Physics blowup | Active ragdoll explodes or goes limp | Constrain physics to track animation (C.1) |
| Floating / clipping feet | Heel floats on slopes, foot clips steps | Foot IK + normal-orient + pelvis adjust (C.2) |
| Spring explosion / dead weight | Mesh flings at low FPS, or reads stiff | Verlet, damped, budgeted spring bones (C.3) |
| Motion-matching memory blowout | Library over budget; search tanks frame | Acceleration structure; learned/compressed MM (C.4) |
| Bone bloat | Crowds CPU/GPU-bound on skinning | Budget bones to deformation need (D.1) |
| Uniform-tolerance foot-slide | Compression slides toes/fingertips | Adaptive error margins by chain depth (D.2) |
| Root/in-place desync | Rubber-banding or skating feet | Tag per clip; reconcile with foot-lock (D.3) |
| Animation-locked | Committed with no way to bail | Choose priority/cancellable per action; buffer (E.2) |
| Inconsistent timing | Same attack varies; reads as buggy | One timing signature per action type (E.1) |

---

## Caveats

- **Skinning-method recommendations are art-directed, not absolute.** The LBS→DQS→CoR ladder is a quality/cost generalization; DQS's bulging means "DQS everywhere" is not strictly better than LBS. Validate on *your* rig's extreme poses.
- **Motion-matching breakpoints don't transfer.** "Over 0.5 GB" and "~70× with LMM" are specific Ubisoft productions; real database size/search cost/responsiveness depend on the motion space, capture quality, and platform. Motion matching also needs a large, expensive-to-capture, well-curated corpus — exactly when Rosen-style procedural animation or a compact state machine wins for small/procedural productions.
- **Active ragdoll is stability-fragile.** It *can* be robust (DReCon, Wolfire) but naive physics-on-animation reliably blows up; production-grade needs constrained tracking (often RL or hand-tuned controllers). High-risk feature needing a human stability pass — not a flag a generator flips safely.
- **The timing/cancel design rules live in `game-feel-and-juice`.** This skill does not decide whether an action should be cancellable, how long an enemy wind-up is, or where the responsiveness ceiling sits. It owns only the system that stores and delivers the chosen timings. To set the values, read `game-feel-and-juice` first.
- **Engine specifics are deliberately omitted.** Per the technical-craft charter, this names the technique, data, and cost model only. Concrete `AnimationTree`/Animator/Anim Blueprint wiring, IK-node setup, and spring-bone components belong in the engine packs (`godot`/`unity`/`unreal`) as a separate authoring pass.
- **AI-authoring adds an invisible-failure risk the literature doesn't cover.** A skinning collapse, a phase-mismatched blend, a foot-slide is obvious to an animator and silent to a headless generator. Every "Test for:" is written to run headlessly precisely because the generator can't *see* the artifact — the validators are the only defense.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
</content>
