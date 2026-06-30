# T3 — Animation Systems: Motion-Architecture Research

> **Research document** — seed for the `animation-systems` technical-craft skill.
> Written by the gamestack research agent; adversarially verified against independent sources.
> Engine-agnostic, design-spec level — no engine code (no `AnimationTree` setup, no Animator graphs),
> only the technique, the data it needs, the cost model, and a "Test for:" criterion per rule.
> Verification tags: ✅ ≥2 independent/primary sources · ⚠️ single source · ❌ refuted or substantially contested.

---

## 1. TL;DR

- **Skinning is a cost/quality dial, not a solved default.** Linear blend skinning (LBS) is cheap and ubiquitous but collapses volume on twist — the "candy-wrapper." Dual-quaternion skinning (DQS) fixes the collapse at extra per-vertex cost but introduces joint-bulging; Optimized Centers of Rotation (Le & Hodgins 2016, Disney) fixes both at higher precompute. Pick by joint type, not globally. ✅
- **Blending architecture is a hierarchy of two complementary tools.** State machines own *discrete* modes (locomotion, combat, climb, dead); blend trees/blend spaces own *continuous* variation within a mode (speed, direction); additive layers and per-bone masks compose *independent* regions (aim offset on the spine while the legs run). They are not competitors — production rigs use all three. ✅
- **Procedural and physics-driven motion buy plausibility per-variant without bespoke clips.** IK foot placement, look-at/aim offsets, spring-bone secondary motion, active ragdoll (DReCon), and motion matching all generate or correct motion at runtime — exactly the leverage a generator needs to make many characters move from a finite hand-authored library. ✅
- **Motion matching trades memory for hand-authoring; Learned Motion Matching trades it back.** Vanilla motion matching needs >0.5 GB of clip+metadata and scales linearly with data; Holden et al.'s Learned Motion Matching (SIGGRAPH 2020) compresses a 590 MB controller to ~8.5 MB of network weights (~70×) while preserving behavior. ✅
- **For a generator, the contract is a finite clip+rig library plus parametric correction.** Hand-author the rig, a fixed set of `(anticipation, active, recovery)` clips, blend-space axes, and IK/secondary-motion parameters; let generation *select and warp* within them. Never let generation invent new bone counts, timing signatures, or uncapped IK chains.

---

## 2. Key Findings

**1. Linear blend skinning's "candy-wrapper" is a mathematical inevitability, not a bug.** ✅
LBS computes each skinned vertex as a weighted average of bone transform *matrices*. Averaging two rotation matrices does not produce a rotation — at a 180° twist the two half-weighted matrices cancel and the surface collapses toward the bone axis, pinching like a twisted candy wrapper (SIGGRAPH 2014 "Skinning: Real-time Shape Deformation" course, Kavan et al.; multiple academic reproductions). The artifact is worst at wrists, forearms, and shoulders. It is cheap (one matrix-vector multiply per influencing bone) which is why it remains the default.

**2. Dual-quaternion skinning fixes volume collapse but is not free and introduces its own artifact.** ✅
DQS blends in dual-quaternion space, which interpolates along the arc of the unit sphere and preserves rigidity, eliminating the candy-wrapper (Kavan et al.; Disney "Enhanced Dual Quaternion Skinning for Production Use," used on *Frozen*). The tradeoff: extra per-vertex math, and a "bulging joint" / distorted-normal artifact on bent joints. The honest framing is a ladder — LBS (cheapest, candy-wrapper) → DQS (fixes twist, bulges) → Optimized Centers of Rotation (Le & Hodgins 2016, fixes both, highest precompute). None reproduce muscle bulge; that needs corrective blend shapes.

**3. IK solvers split cleanly into "analytical and free" vs "iterative and bounded."** ✅
A two-bone IK solver (arm, leg) is analytical — closed-form trigonometry, no iteration, effectively free, so dozens can run per frame. CCD and FABRIK are iterative, converging on a target over multiple passes; they handle long chains (tails, tentacles, spines) but cost scales with chain length × iteration count (MoCap Online IK guide; Godot 4 / Unreal IK docs; academic CCD/FABRIK implementations). Match the solver to the chain: two-bone for limbs, FABRIK/CCD for long chains, look-at for head/aim.

**4. The blend tree / state machine split is a layering pattern, not a choice between tools.** ✅
State machines manage discrete, mutually-exclusive states and the transitions between them; blend trees (1D on one axis like speed; 2D on speed×direction) handle continuous interpolation *inside* a state; additive layers add a delta (aim, lean, hit-react) on top without replacing the base; per-bone masks (Layered Blend Per Bone / Avatar Masks) let an upper-body state machine run in parallel with a lower-body one (Unity/Unreal/Godot animation docs; MoCap Online state-machine patterns). Complex humanoids are routinely an upper/lower split with additive overlays.

**5. Motion matching replaces the hand-built graph with a per-frame database search.** ✅
Instead of authoring states and transitions, motion matching every few frames searches the whole mocap database for the clip frame whose pose (foot positions/velocities, key-bone velocity) and future trajectory best match the desired motion, then jumps there (Simon Clavet, GDC 2016 "Motion Matching and the Road to Next-Gen Animation," shipped in *For Honor*; Naughty Dog's Mach & Zhuravlov, GDC 2021, *The Last of Us Part II* — locomotion, traversal, melee, cover). It yields "high quality, controllable responsiveness, minimal manual work" but needs a large curated mocap corpus and an acceleration structure (KD-tree, LOD) for the search.

**6. Motion matching scales poorly in raw memory; learned variants fix it.** ✅
Memory grows linearly with clip data, and vanilla motion matching can exceed half a gigabyte of clips plus matching metadata (O3DE motion-matching docs; Holden et al.). Learned Motion Matching (Holden, Kanoun, Pérez, SIGGRAPH 2020) replaces the database and search with small neural networks, compressing a 590 MB controller to ~17 MB, and ~8.5 MB after 16-bit quantization — about a 70× memory win — while preserving motion-matching behavior. ✅

**7. Active ragdoll is the modern synthesis of physics and authored motion.** ✅
Passive ragdoll (limbs go fully physics on death) is decades old; *active* ragdoll keeps a physically simulated body that is continuously driven toward an animation target, so characters react to forces while still "trying" to perform authored motion (David Rosen / Wolfire, GDC 2014 "An Indie Approach to Procedural Animation," shown in *Overgrowth*/*Receiver*; Ubisoft's DReCon, Bergamin/Clavet/Holden/Forbes, SIGGRAPH Asia 2019, drives a fully simulated character from a motion-matching kinematic controller via reinforcement learning at low runtime cost). The hard part is stability — naive physics-on-animation "was pretty terrible" until the simulation was constrained to track the animation predictably (Naughty Dog, *Uncharted 4* physics-animation talk).

**8. Procedural IK foot placement is the cheapest, highest-leverage "make it fit the world" trick.** ✅
A small amount of runtime IK — raycast under each foot, then a two-bone solve to plant it on the actual ground and orient it to the slope normal — turns flat-ground walk cycles into terrain-aware locomotion without new clips (Rune Skovbo Johansen, "Automated Semi-Procedural Animation," GDC 2009, the Unity Locomotion System; foot-IK layers in modern engines). "Semi-procedural" = author the cycle, let IK do minimal correction.

**9. Animation compression is two orthogonal techniques with a foot-sliding failure mode.** ✅
Quantization reduces per-channel bit depth (Riot quantizes rotations to 48 bits/quaternion — 15 bits × 3 components + 2 bits to flag the omitted one — for a 0.375 ratio and 0.000043 precision); curve fitting drops redundant keyframes by fitting splines to within an error tolerance (Riot compressed 661 frames to 90). Combined, Riot reaches ~25% of original size. The shared danger: error accumulates down the bone chain, so a tolerance that looks fine at the hip becomes visible foot-sliding at the toe — fix with *adaptive* error margins that tighten for joints with longer descendant chains (Riot Games engineering blog; Unreal/ozz/Unity compression docs). ✅

**10. Root motion and in-place locomotion fail in opposite directions.** ✅
Root motion bakes displacement into the clip — authentic weight and curved paths, but the game cedes velocity control and any mismatch with code-driven movement causes drift/desync. In-place keeps the character pinned and code moves it — full control, but if code speed ≠ clip's implied speed the feet skate ("foot sliding") (polycount/engine-community consensus; Clavet uses timescale + foot-locking warps to reconcile them). Use root motion for committed set-piece moves (vaults, takedowns), in-place + code for free locomotion, and reconcile with foot-lock IK.

---

## 3. Details by Sub-domain

---

### Sub-domain A — Skeletal Animation Fundamentals

Covers: bones/joints and the skeleton hierarchy, skinning (LBS → DQS → CoR), and forward vs inverse kinematics with the right IK solver per chain.

**Foundation:** a skeleton is a tree of joints; each joint holds a local transform relative to its parent, and the final world transform is the product down the chain. A *bind pose* records the rest skeleton; skinning maps the deformed skeleton back onto the mesh via per-vertex bone weights.

#### A.1 Choose the skinning method per joint, on the volume-vs-cost ladder

**Rule:** Default to linear blend skinning; escalate to dual-quaternion skinning only on joints that twist hard (wrists, forearms, shoulders, necks); reserve Optimized Centers of Rotation for hero characters where both candy-wrapper and joint-bulge must vanish. Never pick one method globally without checking the twisting joints.

- **Exemplar / source:** SIGGRAPH 2014 "Skinning: Real-Time Shape Deformation" course (Kavan, Jacobson, et al.) is the canonical derivation of LBS collapse and DQS; Walt Disney Animation's "Enhanced Dual Quaternion Skinning for Production Use" took DQS to film production (*Frozen*); Le & Hodgins, "Real-Time Skeletal Skinning with Optimized Centers of Rotation" (Disney Research, 2016) precomputes a per-vertex rotation center to fix both LBS and DQS artifacts.
- **Test for:** twist the most extreme joint (forearm/wrist) to ±180° in the bind pose and inspect the silhouette — LBS shows a pinch toward the bone axis (candy-wrapper); DQS shows a bulge at the bend. Confirm the chosen method has no visible collapse at the *worst* joint, not the average one.
- **Failure mode — global skinning choice:** picking DQS everywhere for "quality" pays its cost and bulging artifact on every joint including those that never twist; picking LBS everywhere ships the candy-wrapper on every wrist. Neither reproduces muscle bulge — that requires corrective blend shapes layered on top.

#### A.2 Use forward kinematics for authored poses, inverse kinematics for world contact

**Rule:** Let FK (play the clip's joint rotations down the chain) drive the *authored* motion; layer IK (solve joint rotations from a desired end-effector position) only where the body must touch a runtime-variable target — ground, ledge, aim direction, look target. IK is a *correction* on top of FK, not a replacement for it.

- **Exemplar / source:** foot placement, hand-on-ledge, look-at, and weapon-aim offsets are the canonical IK uses (MoCap Online "Inverse Kinematics in Games"; engine IK docs). Aim offsets are a 2D blend space pitching/yawing the upper body; look-at is a constraint tracking a focus target, blended in by awareness.
- **Test for:** disable all IK and confirm the base FK animation still reads as correct on flat ground / neutral aim (IK must be additive correction). Then enable IK and confirm feet plant on slopes and the aim reticle aligns with the actual weapon muzzle.
- **Failure mode — IK fighting FK:** an IK target that pulls a joint past the clip's intent produces popping or hyperextension; IK whose blend weight snaps 0→1 produces a visible jerk. Blend IK in/out over several frames and clamp joint limits.

#### A.3 Match the IK solver to the chain length

**Rule:** Use an analytical two-bone solver for limbs (arms, legs); use an iterative solver (FABRIK or CCD) for long chains (spines, tails, tentacles); use a look-at constraint for single-joint aim/head tracking. Don't run an iterative full-body solver where a two-bone solve suffices.

- **Exemplar / source:** two-bone IK is closed-form trigonometry — no iteration, trivial cost, "essentially free," so dozens run simultaneously; FABRIK and CCD iterate to convergence and cost scales with chain length × iterations (MoCap Online; Godot 4 IK modifier stack of two-bone/FABRIK/CCDIK/spline/look-at; academic CCD & FABRIK implementations).
- **Test for:** count IK chains and their solver type in a frame; confirm every 2-segment limb uses the analytical solver and only genuinely long chains use an iterative one, with a capped iteration count.
- **Failure mode — solver mismatch:** an iterative solver on every limb burns CPU for no quality gain; too few iterations on a long chain leaves the end-effector short of its target (visible reach failure); too many is wasted frame time.

#### A.4 Retarget one clip library across many skeletons instead of authoring per-character

**Rule:** Author animation once on a reference skeleton and *retarget* it onto every character that shares a compatible bone hierarchy, compensating for different limb proportions and preserving foot/hand contacts — so a finite clip library covers an unbounded roster. Standardize on one skeleton/naming convention up front; retargeting quality degrades fast across mismatched hierarchies.

- **Exemplar / source:** retargeting maps bone names source→target, scales for proportion differences (ratio of bone lengths), preserves foot contacts despite height differences, and adapts joint ranges; every major tool ships it — Unity Humanoid Avatar, Unreal IK Retargeter, MotionBuilder, Maya HumanIK (MoCap Online retargeting guide; engine retargeting docs). A consistent skeleton structure is what makes a clip library "work across the entire project without per-character adjustments."
- **Test for:** apply the same locomotion + action set to the shortest and tallest characters; confirm feet still contact ground, hands still reach their props, and no limb hyperextends. Confirm all rigs share the reference hierarchy/naming.
- **Failure mode — retarget breakage:** mismatched bone hierarchies or naming produce twisted limbs, broken foot contact, or self-intersection; per-character bespoke clips defeat the entire reuse model and balloon memory.

**→ Procedural / headless implication.** Retargeting *is* the combinatorial-reuse mechanism: the rig + a single clip library are the frozen contract, and a generator dresses an unbounded roster by retargeting that library onto skeletons sharing the bind hierarchy — it must not invent bone counts or weights per-asset (skinning artifacts are invisible to a headless generator and ship silently). Expose the reference skeleton, skinning method, and IK-chain definitions as per-rig data the generator selects from a vetted set, never computes. Validate generated skins by twisting the worst joint headlessly and rejecting silhouette collapse beyond a threshold, and validate retargets on proportion extremes.

---

### Sub-domain B — Animation Blending & State Architecture

Covers: 1D/2D blend spaces, animation state machines, additive layers, and per-bone (upper/lower) splits — and the data a designer must expose for these to compose safely.

#### B.1 Put discrete modes in a state machine, continuous variation in a blend tree

**Rule:** Model mutually-exclusive behaviors (Idle/Locomotion/Combat/Climb/Swim/Dead) as states with explicit transitions and conditions; model smooth interpolation within a behavior (idle→walk→run by speed) as a blend tree. Don't encode a continuous axis as a fan of discrete states, and don't encode a hard mode-switch as a blend.

- **Exemplar / source:** the standard locomotion 1D blend tree distributes idle/walk/jog/run/sprint along a speed axis; the 2D blend tree blends a grid on speed×direction so a character strafes without turning to face travel (Unity/Unreal/Godot blend-tree docs; MoCap Online). State machines own Locomotion vs Combat vs Dead and the transition rules.
- **Test for:** sweep the input axis (speed 0→max) and confirm the blend output is continuous with no pop; trigger each discrete transition and confirm it fires on its condition with a defined blend duration.
- **Failure mode — state explosion / blend misuse:** representing every speed as its own state yields an unmaintainable graph and popping at boundaries; blending across a true mode change (alive→dead) produces a nonsensical half-state.

#### B.2 Build 2D blend spaces on orthogonal, normalized axes

**Rule:** Choose blend-space axes that are independent and normalized (e.g. forward/back velocity × left/right velocity, or speed × turn-rate); place sample clips at the extreme and zero points; keep the sampled clips phase-synchronized so feet line up when blended.

- **Exemplar / source:** strafe locomotion sets are authored as a 2D grid (8-way directional walk/run) and blended by the movement vector (engine blend-space docs). Phase sync (aligning the footfall timing of all clips in the set) is what prevents the blend from averaging a left-foot-down clip with a right-foot-down clip into a hover.
- **Test for:** drive the blend to interior points (e.g. diagonal half-speed) and confirm feet still contact in a plausible cadence; confirm the four cardinal extremes match their authored clips exactly.
- **Failure mode — unsynchronized blend:** clips at different footfall phases blend into "moonwalking" / sliding feet; non-orthogonal axes make some input combinations unreachable or double-counted.

#### B.3 Layer independent body regions with additive animation and bone masks

**Rule:** Express overlays that must combine with *any* base motion (aim offset, lean, hit-react, breathing, facial) as *additive* layers — a delta from a reference pose added on top — and isolate body regions with per-bone masks so an upper-body state machine runs in parallel with lower-body locomotion.

- **Exemplar / source:** additive aim offsets, hit reactions, and facial overlays layer over any locomotion state; the upper/lower split (Layered Blend Per Bone in Unreal, Avatar Masks in Unity) is the foundational pattern for characters that must aim while running (engine layered-animation docs; MoCap Online state-machine patterns).
- **Test for:** play every lower-body locomotion state while firing each upper-body overlay; confirm the overlay reads correctly on top of all of them and the mask boundary (usually the spine) shows no kink.
- **Failure mode — double-driven or torn bones:** two layers writing the same bone without a mask fight and jitter; a mask boundary mid-spine with no blend produces a visible "broken back" seam.

**→ Procedural / headless implication.** Blend architecture is highly generator-friendly *because* it is combinatorial: a finite set of clips × blend axes × additive overlays yields a large reachable motion space without new clips. Expose the axes and the additive set as data; require all clips in a blend set to share a footfall-phase tag so a generator can assemble new sets safely. The validator rejects any blend set with mixed phase tags or an additive overlay missing its reference pose.

---

### Sub-domain C — Procedural & Physics-Driven Motion

Covers: ragdoll and active ragdoll, procedural IK foot/hand placement on uneven terrain, procedural secondary motion (spring bones / cloth / hair), and motion matching as a data-driven alternative to hand-built state machines.

#### C.1 Use active ragdoll, not passive ragdoll, when reactions must stay characterful

**Rule:** For death or full loss of control, passive ragdoll (hand the skeleton to the physics solver) is fine. For *reactions* during gameplay — stagger, push, partial hit — drive a physically simulated body toward the authored animation target (active ragdoll / powered constraints) so the character reacts to force while still performing intended motion. Constrain physics to track animation; never run physics open-loop on gameplay-authored poses.

- **Exemplar / source:** David Rosen / Wolfire (GDC 2014) built active ragdolls in *Overgrowth* from very few keyframes blended with Newtonian physics; Ubisoft's DReCon (SIGGRAPH Asia 2019) trains an RL controller so a *fully* simulated character tracks a motion-matching kinematic target responsively at low runtime cost; Naughty Dog (*Uncharted 4*) found naive physics-on-animation "pretty terrible" until they drove physics objects *from* animation "with minimal distortions and almost absolute artistic control."
- **Test for:** apply an impulse mid-animation and confirm the body deforms believably *and* recovers to the authored pose; cut all external force and confirm the simulated body converges back onto the FK target with no residual jitter.
- **Failure mode — unconstrained physics blowup:** physics driven open-loop on non-physical gameplay poses explodes, jitters, or goes limp at the wrong moment; over-stiff tracking constraints make the reaction invisible (no point simulating).

#### C.2 Plant feet with runtime IK before adding more clips

**Rule:** Before authoring slope/step-specific clips, add foot-placement IK: raycast beneath each foot, move the foot to the hit point, orient it to the surface normal, and adjust the pelvis height so no leg over-extends. This adapts flat-ground cycles to arbitrary terrain at near-zero authoring cost.

- **Exemplar / source:** Rune Skovbo Johansen's Locomotion System (GDC 2009, "Dynamic Walking with Semi-Procedural Animation"; thesis "Automated Semi-Procedural Animation for Character Locomotion") analyzes input cycles into a velocity map, blends them, and "adjusts the movements of the bones in the legs to ensure that the feet step correctly on the ground" across slopes and steps — adjusting clips "minimally."
- **Test for:** walk the character across a slope, a single step, and a gap edge; confirm both feet contact the actual surface, the foot orients to the slope, and no knee hyperextends or pops. Toggle IK off to confirm the base cycle is what IK is correcting.
- **Failure mode — floating/clipping feet & pelvis pop:** foot IK that ignores pelvis height over-extends the leg on a step; IK that snaps without blending pops; IK with no normal-orientation leaves the foot flat on a slope (heel floats).

#### C.3 Add secondary motion with spring bones, integrated stably and budgeted

**Rule:** Drive hair, cloth, tails, antennae, pouches, and soft tissue with lightweight spring/jiggle bones (a chain where the base animation moves the root and a spring/damper drags the rest), integrated with a stable scheme (Verlet) so it doesn't explode at low frame rates. Budget the bone count — secondary motion is "free-looking" but not free.

- **Exemplar / source:** spring-bone / jiggle-bone systems give hair, cloth, and accessories inertial sway responding to head turns, wind, and body movement; Verlet integration is preferred for stability and performance (multiple implementation references; the technique is standard across engines).
- **Test for:** stop the character abruptly and confirm secondary elements overshoot and settle (follow-through), not snap; drop the frame rate and confirm the simulation stays bounded (no divergence); confirm the secondary-bone budget stays within the skinning cost ceiling.
- **Failure mode — explosion or dead weight:** under-damped springs at low FPS diverge and fling the mesh; over-damped springs read as stiff/dead; uncapped spring-bone counts blow the per-character bone budget (see D.1).

#### C.4 Reach for motion matching when authored graphs can't cover the motion space

**Rule:** When the required motion space is too large or too responsive to hand-author (open traversal, nuanced combat footwork), consider motion matching: every few frames, search a curated mocap database for the frame best matching current pose + desired trajectory, jump there, and warp procedurally (timescale, foot-lock, slope) to hit exact gameplay constraints. Budget the database cost up front.

- **Exemplar / source:** Clavet (GDC 2016) shipped it in *For Honor* — "every frame, look at all mocap and jump at the best place," switching multiple times per second, with procedural rotation/timescale/foot-lock/slope warps; Naughty Dog's *The Last of Us Part II* (GDC 2021) used it for locomotion, traversal, melee, and cover. Holden et al.'s Learned Motion Matching (SIGGRAPH 2020) compresses the controller ~70× (590 MB → ~8.5 MB).
- **Test for:** confirm a curated, marked-up database exists and an acceleration structure (KD-tree/LOD) bounds the search cost per frame; measure responsiveness (input→matched-pose latency) against the feel budget; measure memory and confirm it fits the platform.
- **Failure mode — data starvation & memory blowout:** too small/uncurated a database makes matches snap or drift; the database grows past the memory budget (linear scaling) on a content-rich game; brute-force search with no acceleration structure tanks the frame. Learned/compressed variants or a hybrid with a small state machine are the mitigations.

**→ Procedural / headless implication.** This sub-domain is the generator's best friend: IK, spring bones, active ragdoll, and motion matching all produce *per-variant plausible motion from shared inputs*. A generated creature can move by retargeting a base locomotion set + foot IK + spring-bone tails, with no hand-keyed clips per variant. Motion matching lets generated characters draw from one shared corpus. But the corpus, the IK parameters, the spring budgets, and the active-ragdoll constraints are all hand-authored contracts — the generator varies inputs within them and is validated against stability/budget thresholds, never authors new physics constants.

---

### Sub-domain D — The Animation Pipeline & Cost Model

Covers: animation compression and memory cost, the bone-count / skinning-cost budget, and root motion vs in-place movement.

#### D.1 Budget bone count against skinning cost, not just memory

**Rule:** Treat the per-character bone count as a hard budget: it drives both animation memory (each bone is a transform track per frame) and per-frame skinning/pose cost (the expensive work is applying transforms during mesh update). Add bones only where deformation demands; share a skeleton across characters where possible.

- **Exemplar / source:** doubling bone count roughly doubles animation memory and often needs more keyframes for the finer motion; the dominant runtime cost of skinning is applying transforms during mesh updates, which scales with bone (and vertex) count, not with keyframe count (skeletal-animation optimization references; engine docs).
- **Test for:** confirm each rig's bone count is within the platform budget and that bones exist where deformation needs them (no decorative bones bloating every clip's memory). Profile pose+skinning time under the maximum simultaneous on-screen character count.
- **Failure mode — bone bloat:** a high bone count "for flexibility" multiplies every clip's memory and every frame's skinning cost across every instance; crowds of high-bone characters become CPU/GPU-bound on the skinning pass.

#### D.2 Compress with quantization + curve fitting, guarded by adaptive error margins

**Rule:** Apply both compression families — quantize per-channel bit depth, and curve-fit to drop redundant keyframes within an error tolerance — but make the tolerance *adaptive*: tighten it for joints with longer descendant chains, because error compounds down the chain and surfaces as foot-sliding at the leaf.

- **Exemplar / source:** Riot quantizes rotations to 48 bits/quaternion (15×3 components + 2 bits flagging the largest, omitted, component; 0.375 ratio; 0.000043 precision) and curve-fits with Catmull-Rom splines, inserting keys at the point of maximum error (661 frames → 90), reaching ~25% of original size; they "tighten the error threshold if the joint has a longer chain of descendants" to kill foot-sliding (Riot Games engineering blog). Quantization is load-time cheap; curve fitting is a heavy preprocess.
- **Test for:** compare compressed vs source on the end-effectors (hands, feet), not the root; confirm foot-sliding and hand-contact drift stay under a perceptual threshold. Confirm error tolerance scales with descendant-chain length.
- **Failure mode — uniform tolerance foot-slide:** a single global error tolerance that looks fine at the hip accumulates into visible sliding/contact-break at toes and fingertips; over-aggressive keyframe removal flattens snappy motion into mush.

#### D.3 Choose root motion vs in-place per clip, and reconcile with foot-lock

**Rule:** Use root motion for committed, displacement-exact moves (vaults, mantles, takedowns, turn-in-place) where authored travel must match the animation; use in-place + code-driven velocity for free locomotion where the game needs control. Reconcile either with foot-lock IK and timescale so authored stride matches actual ground speed.

- **Exemplar / source:** root motion gives authentic weight and curved paths but cedes velocity control and desyncs if code overrides it; in-place gives control but skates feet when code speed ≠ clip speed (engine-community consensus). Clavet's motion-matching warps (timescale to hit target speed, foot-locking when the source is stationary, slope warping) are the production reconciliation.
- **Test for:** for each locomotion clip, measure foot-sliding (world-space foot velocity during stance should be ~0); for each root-motion set-piece, confirm end position matches the gameplay target with no rubber-banding.
- **Failure mode — desync both ways:** root motion fighting a code-driven position causes rubber-banding/warping; in-place at the wrong speed skates the feet. A move authored as one but consumed as the other (e.g. root-motion vault treated as in-place) teleports or slides.

**→ Procedural / headless implication.** The cost model is the generator's guardrail. Bone-count budgets, compression tolerances, and the root-motion/in-place tag are per-clip *metadata* the generator must respect, not recompute. A generated character that exceeds the bone budget, ships uncompressed clips, or mislabels a root-motion clip is malformed — validate headlessly: reject over-budget rigs, measure stance-phase foot velocity to catch sliding, and confirm every clip carries a motion-type tag.

---

### Sub-domain E — Readability & Timing

Covers: how the *system* delivers the anticipation/follow-through timing that `game-feel-and-juice` specifies as a design rule, and the animation-canceling vs responsiveness tradeoff (the "animation-locked" failure mode).

> **Division of labor (do not re-litigate here):** the *design rule* — that anticipation reads as a fair warning on enemies but as input lag on the player avatar, and that follow-through sells weight without extending recovery — is owned by `game-feel-and-juice` (its 12-animation-principles section, drawing on Thomas & Johnston and Richard Williams' *The Animator's Survival Kit* for the underlying timing/spacing/weight craft). This skill owns the *system architecture* that produces that timing reliably: the `(anticipation, active, recovery)` clip decomposition, the cancel windows, and the blend that returns control fast while the visual follows through.

#### E.1 Author every action as a timed `(anticipation, active, recovery)` triplet

**Rule:** Decompose each action animation into three explicitly-tagged phases — anticipation (wind-up), active (the committed/contact frames), recovery (settle) — with the durations as exposed data, so the same action shares one timing signature everywhere and gameplay can key off phase boundaries (hit-active windows, cancel points).

- **Exemplar / source:** Richard Williams (*The Animator's Survival Kit*) grounds the timing/spacing/weight that makes anticipation and follow-through read; game animation systems realize it as phase-tagged clips whose boundaries gameplay queries. `game-feel-and-juice` specifies the enemy-vs-player asymmetry; the system stores the phase durations per action so all generated enemies of a type share the signature.
- **Test for:** confirm each action exposes anticipation/active/recovery boundaries as data; confirm the same action type uses one consistent timing signature across all characters that share it (Principle 9 — consistency enables mastery); confirm follow-through VFX/secondary motion outlasts the recovery phase (control returns before the visual fully settles).
- **Failure mode — inconsistent or merged phases:** randomized per-instance timing reads as buggy and blocks mastery; an action with no distinct anticipation phase can't be telegraphed (enemy) or feels laggy (player) — but *which* of those is the bug is a `game-feel` design call, not a system call.

#### E.2 Decide animation-priority vs cancellable offense explicitly, and tune cancel windows

**Rule:** Decide, per action, whether it is *animation-priority* (plays to completion before the next input fires — commitment, weight, risk) or *cancellable* (can be interrupted into another action — fluid, responsive). Expose cancel windows as data on the recovery phase, and pair animation-priority with input buffering so the *next* input still feels honored.

- **Exemplar / source:** the action-combat design split between "animation priority" (attacks complete their frames; stronger attacks have more startup/recovery, creating rhythm and risk) and "cancellable offense" (instant-feeling chaining of attacks/dodges regardless of mid-animation state) is the explicit axis combat designers tune (action-game combat-design discourse; fighting-game cancel theory). Canceling = interrupting an in-progress animation with another, showing part of the first and all of the second.
- **Test for:** for each action, confirm its commitment model is a deliberate tag (not an accident of clip length); confirm cancel windows are data on the recovery phase; confirm input buffering carries an input pressed during a non-cancellable action to its first legal frame (cross-ref `game-feel-and-juice` B.1).
- **Failure mode — the "animation-locked" trap:** long, non-cancellable recovery with no buffering and no dodge-out makes combat feel unresponsive and unfair ("committed to a block/attack with no way to bail"); the opposite extreme — everything cancellable into everything — erases weight and risk. Both are failures of an *unchosen* default; the right point on the axis is a per-game design decision.

**→ Procedural / headless implication.** Timing and cancel data are a hand-authored contract the generator selects from, never invents. Author a fixed library of phase-tagged action clips with one timing signature per action type and explicit cancel-window/commitment tags; a generator assigns library entries to generated characters/moves but must not compute new timings or cancel windows. Validate that every generated action references a library entry and that all instances of an action type share its signature — the same `animation-state validator` `game-feel-and-juice` specifies.

---

## 4. Recommendations

**Stage 1 — Rig & skinning contract (before any clips or generation):**
1. Freeze the skeleton hierarchy and a shared bind pose; set the per-character bone-count budget against the *skinning* cost on target hardware at max on-screen character count, not just memory.
2. Choose skinning method per joint group: LBS default, DQS on hard-twisting joints, CoR only for hero characters; verify by twisting the worst joint to ±180°.
3. Define IK chains and solvers: two-bone for limbs, FABRIK/CCD (capped iterations) for long chains, look-at for aim/head. IK is correction layered on FK.

**Stage 2 — Blend architecture (on a playable rig):**
4. Lay out the state machine (discrete modes + transitions) and, inside each, the 1D/2D blend trees on orthogonal normalized axes.
5. Phase-tag every clip in a blend set (footfall timing) so blends don't skate; build the upper/lower split with additive overlays (aim, hit-react, lean) and bone masks.
6. Tag every locomotion/action clip as root-motion or in-place; add foot-lock IK to reconcile authored stride with code-driven speed.

**Stage 3 — Procedural & physics layer (on feature-complete locomotion):**
7. Add foot-placement IK (raycast + two-bone solve + pelvis adjust + normal orient) before authoring terrain-specific clips.
8. Add spring-bone secondary motion (Verlet, damped, budgeted) for hair/cloth/tails; test for overshoot-and-settle and low-FPS stability.
9. Add active ragdoll for reactions (physics tracking the animation target, constrained); reserve passive ragdoll for death.
10. If the motion space is too large/responsive for an authored graph, evaluate motion matching — only with a curated database, an acceleration structure, and a memory budget; consider a learned/compressed variant.

**Stage 4 — Pipeline & cost (ship pass):**
11. Compress with quantization + curve fitting; use adaptive error margins (tighter for longer descendant chains); validate on end-effectors for foot-sliding/contact drift.
12. Decompose actions into `(anticipation, active, recovery)` triplets with exposed phase boundaries and explicit commitment/cancel tags; pair animation-priority moves with input buffering.

**Stage 5 — Procedural-readiness validators (block commit on failure):**
13. **Rig/skin validator** — reject generated rigs that change bone count/weights off-contract or collapse the worst joint's silhouette beyond threshold.
14. **Blend-set validator** — reject blend sets with mixed footfall-phase tags or additive overlays missing a reference pose.
15. **Cost validator** — reject over-budget bone counts, uncompressed/mis-tagged (root-motion vs in-place) clips, and clips whose stance-phase foot velocity exceeds the sliding threshold.
16. **Animation-state validator** (shared with `game-feel-and-juice`) — every generated action references a library `(anticipation, active, recovery)` entry; all instances of an action type share one timing signature; no generator-computed timings.

**Thresholds that change the plan:**
- **Crowds / many simultaneous characters:** drop to LBS + low bone count + aggressive compression + shared skeletons; skip per-character active ragdoll; motion matching is usually too memory-heavy — use a compact state machine or LMM.
- **Hero/cinematic character:** CoR skinning + corrective blend shapes + active ragdoll + motion matching are justified; budget the memory and CPU explicitly.
- **Stylized / low-fidelity art:** procedural animation (Rosen's few-keyframe approach) and aggressive IK can replace large mocap libraries entirely — and read as *intentional* rather than as a shortcut.
- **2D / sprite or cutout rigs:** skinning-method choices collapse; the blend/state-machine/phase-tag and timing-triplet structure still apply, as does squash/stretch (owned by `game-feel-and-juice`).

---

## 5. Caveats

- **Skinning-method recommendations are art-directed, not absolute.** The LBS→DQS→CoR ladder is a quality/cost generalization; the right choice depends on silhouette, target hardware, and whether corrective blend shapes are in the budget. DQS's bulging and distorted-normal artifacts mean "DQS everywhere" is not strictly better than LBS. Validate on the actual extreme poses of *your* rig.
- **Motion-matching breakpoints don't transfer.** "Over half a gigabyte" and "~70× compression with LMM" are reported for specific Ubisoft productions; the actual database size, search cost, and responsiveness depend on the motion space, capture quality, and platform. Motion matching also demands a large, well-curated, expensive-to-capture mocap corpus — a cost small/procedural productions often can't pay, which is exactly when Rosen-style procedural animation or a compact state machine wins.
- **Active ragdoll is stability-fragile.** DReCon and Wolfire show it *can* be robust, but naive physics-on-animation reliably blows up; production-grade active ragdoll needs constrained tracking (and often RL or hand-tuned controllers). Treat it as a high-risk feature needing a human stability pass, not a parameter a generator can flip on safely.
- **The timing/cancel design rules live in `game-feel-and-juice`.** This skill deliberately does not decide whether a given action should be cancellable, how long an enemy wind-up should be, or where the responsiveness ceiling sits — those are feel/design calls. It owns only the *system* that stores and delivers the chosen timings. If you came here to decide the timing values, read `game-feel-and-juice` first.
- **Engine specifics are deliberately omitted.** Per the technical-craft charter, this doc names the technique, data, and cost model only. The concrete `AnimationTree` / Animator / Anim Blueprint wiring, IK-node setup, and spring-bone components belong in the engine packs (`godot`/`unity`/`unreal`) as a separate authoring pass.
- **AI-authoring adds an invisible-failure risk the source literature doesn't cover.** A skinning collapse, a phase-mismatched blend, or a foot-slide is obvious to a human animator and silent to a headless generator. Every "Test for:" here is written to be run headlessly precisely because the generator can't *see* the artifact — the validators are the only line of defense.

---

## Relationship to `game-feel-and-juice` and sibling skills

**This skill is the implementation layer under `game-feel-and-juice`'s animation-timing principles.** `game-feel-and-juice` owns the *design rules*: the player-vs-enemy anticipation inversion, follow-through as weight-without-recovery-cost, the 12 principles applied to games, squash/stretch, and the responsiveness ceiling. `animation-systems` owns the *motion-system architecture* that delivers those rules reliably and at scale: the rig and skinning, blend trees / state machines / additive layers, IK, procedural and physics-driven motion, the compression/cost model, and the `(anticipation, active, recovery)` clip decomposition with cancel windows.

The two share one validator — the `animation-state validator` that confirms generated actions draw from a fixed library and share one timing signature per action type. When designing, apply `game-feel-and-juice` first (what should the timing *feel* like?), then `animation-systems` (what system produces that timing across many generated characters?). They do not overlap: `animation-systems` cites *up* to `game-feel-and-juice` for the design rule and never re-decides it.

Adjacent hand-offs: silhouette/readability of poses → `art-direction-and-readability`; the combat-specific application (telegraphing as fairness, hit-stop per damage tier) → `combat-design`; the rendering substrate for motion VFX (trails, dissolves) → `shaders-and-vfx`; generated *content* coherence (vs generated *geometry*/motion) → `procedural-generation` / `procgen-review`.

---

## Sources

Primary / strongest:

- Clavet, Simon. "Motion Matching and The Road to Next-Gen Animation." GDC 2016 (Ubisoft Montréal, *For Honor*). GDC Vault: https://gdcvault.com/play/1023280/Motion-Matching-and-The-Road · transcript: https://archive.org/stream/GDC2016Clavet/GDC2016-Clavet_djvu.txt
- Holden, Daniel, Oussama Kanoun, Maksym Perepichka, Tiberiu Popa. "Learned Motion Matching." SIGGRAPH 2020 / ACM TOG 39(4). https://theorangeduck.com/media/uploads/other_stuff/Learned_Motion_Matching.pdf · https://www.ubisoft.com/en-us/studio/laforge/news/3NWqwcMU9lMfumgfPAEfka/learned-motion-matching
- Bergamin, Kevin, Simon Clavet, Daniel Holden, James Richard Forbes. "DReCon: Data-Driven Responsive Control of Physics-Based Characters." SIGGRAPH Asia 2019 / ACM TOG 38(6). https://theorangeduck.com/media/uploads/other_stuff/DReCon.pdf
- Mach, Michal, and Maksym Zhuravlov. "Motion Matching in 'The Last of Us Part II'." GDC 2021 (Naughty Dog). https://www.gdcvault.com/play/1027118/Motion-Matching-in-The-Last
- Rosen, David (Wolfire Games). "Animation Bootcamp: An Indie Approach to Procedural Animation." GDC 2014. https://www.gdcvault.com/play/1020583/Animation-Bootcamp-An-Indie-Approach · blog: http://blog.wolfire.com/2014/05/GDC-2014-Procedural-Animation-Video
- Johansen, Rune Skovbo. "Automated Semi-Procedural Animation for Character Locomotion" (thesis) and "Dynamic Walking with Semi-Procedural Animation," GDC 2009. https://runevision.com/tech/locomotion/ · https://www.gdcvault.com/play/966/Dynamic-Walking-with-Semi-Procedural
- Kavan, Ladislav, et al. "Skinning: Real-Time Shape Deformation." SIGGRAPH 2014 course. https://skinning.org/direct-methods.pdf
- Walt Disney Animation Studios. "Enhanced Dual Quaternion Skinning for Production Use." https://disneyanimation.com/publications/enhanced-dual-quaternion-skinning-for-production-use/
- Le, Binh Huy, and Jessica K. Hodgins. "Real-Time Skeletal Skinning with Optimized Centers of Rotation." Disney Research, SIGGRAPH 2016. https://www.semanticscholar.org/paper/Real-time-skeletal-skinning-with-optimized-centers-Le-Hodgins/efa775a35670f53bfeb023576973b53570b125bb
- Riot Games. "Compressing Skeletal Animation Data." Riot Games Engineering. https://www.riotgames.com/en/news/compressing-skeletal-animation-data
- Williams, Richard. *The Animator's Survival Kit*. Faber & Faber, 2001 (expanded 2009). (Timing, spacing, weight; underlying anticipation/follow-through craft — design rule owned by `game-feel-and-juice`.)

Supporting / community & engine docs (for tradeoffs, not as code references):

- "Motion Matching in O3DE, a Data-Driven Animation Technique." https://docs.o3de.org/blog/posts/blog-motionmatching/ (memory scaling)
- MoCap Online. "Inverse Kinematics in Games Guide," "Blend Trees / Animation State Machine Patterns," and "Animation Retargeting." https://mocaponline.com/blogs/mocap-news/inverse-kinematics-games-guide · https://mocaponline.com/blogs/mocap-news/animation-blend-tree-guide · https://mocaponline.com/blogs/mocap-news/animation-retargeting-guide
- Unity / Unreal / Godot animation documentation — blend trees, animation layers, layered blend per bone / avatar masks, IK modifiers (tradeoffs only).
- "Compressing Skeletal Animation" / ozz-animation optimize sample. https://guillaumeblanc.github.io/ozz-animation/samples/optimize/
- Action-game combat-design discourse on animation priority vs cancellable offense (ResetEra design thread; SuperCombo / fighting-game cancel theory) — for the commitment/cancel axis.
- Naughty Dog. "Physics Animation in 'Uncharted 4: A Thief's End'." GDC. https://gdcvault.com/play/1024594/Physics-Animation-in-Uncharted-4
</content>
</invoke>
