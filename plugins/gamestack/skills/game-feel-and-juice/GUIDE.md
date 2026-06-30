# Game Feel & Juice — Guide

The discipline of making an action feel good: tight control, a consistent simulation, and a tuned feedback layer. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Steve Swink's *Game Feel* (2009), the empirical juiciness studies (Dominic Kao 2020/2024), the animation-principles lineage (Thomas & Johnston → Cooper, Totten), Maddy Thorson's Celeste forgiveness write-up, Nijman's "Art of Screenshake," and impact-feel research (Lin et al. 2022; Pichlmair & Johansen 2020). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round1-universal/U2`).

> **The central law:** *fix feel before you juice it, and juice has a ceiling.* Control and simulation come first; polish is layered on a foundation that already feels right; total feedback is tuned to a Medium–High band and the biggest effects are reserved for the rarest moments.

> For the general theory underneath (interesting decisions, flow, reward schedules) see `game-design-fundamentals`. For the combat-specific application (hit-stop per damage tier, telegraphing) see `combat-design` — it cites *up* to this skill.

---

# Sub-domain A — Swink's Three Building Blocks

> Steve Swink, *Game Feel* (2009): game feel is **real-time control of a virtual object in a simulated space, with interactions emphasized by polish.** All three are required; polish alone is cosmetic. ✅ A frequent error (Celia Wagar, 2020 ⚠️) is conflating *feel* (control + simulation) with *juice* (polish) with *design* (mechanics/difficulty) — fix them in that order.

## A.1 Real-time control — keep the correction cycle under the perceptual threshold

The loop *read feedback → decide → act → new feedback* must close fast enough that the player experiences seamless interaction, not discrete actions. Swink's baseline is <100 ms; modern action genres need tighter. ✅ (*Super Mario 64* — "Mario always does what I told him"; the virtuosity is in the loop, not a tutorial.)

- **Latency thresholds (✅ Raaen 2014 + others):** expert FPS ~15 ms; platformers degrade ~50 ms; most action genres annoy at ~100 ms; RTS tolerates ~500–1000 ms; UI transitions 100–200 ms ideal, >400 ms sluggish. **Match the budget to the genre's action quantum** (steering a body vs. issuing an order).
- **Test for:** total controller-input → rendered-pixel latency ≤ the genre threshold on target hardware at target framerate (≤50 ms for action @60fps). Measure with a high-speed camera, not a guess.
- **Failure mode — Floaty/unresponsive controls:** latency past threshold breaks the cause-effect loop; the player stops inhabiting the avatar. Most common real cause is frame-time spikes (uncapped render, load hitches), not game logic.

## A.2 Simulated space — give the avatar consistent, learnable physics

Mass, speed, gravity, inertia, friction, collision — coherent and **constant** so the player can model and predict it. ✅ (*Hollow Knight*: one physics model learned in five minutes stays valid to the final boss.)

- **Test for:** physics parameters are version-controlled constants, not per-scene variables. Any intentional change (low-gravity zone) is signposted before the player commits to a move.
- **Failure mode — Zone-to-zone float:** internalized jump arcs go invalid in a new area; misses feel unfair, not challenging.
- **Genre note:** a puzzle/card game has no avatar physics but still needs a predictable *system* simulation (match rules, cascade logic, tile gravity). The building block is "the rules of the simulated space are predictable."

## A.3 Polish — emphasize what happens; never substitute for it

Audiovisual effects make interactions perceptible and satisfying without changing the simulation. Defer polish until feel is already correct. ✅ (*Celeste*: dust, hair follow-through, dash flash all *confirm* physics events that already feel right.)

- **Test for:** strip all polish (mute, kill particles, disable screen effects). If the core is unplayable without effects, the simulation is too thin and polish is masking it.
- **Failure mode — Polish masking bad fundamentals:** particle storms and shake hiding input lag or inconsistent physics.

**→ Procedural / headless implication.** A generator must not break the feel contract: dense particles or pathfinding spikes that push frame time over the 60fps floor break feel regardless of visual quality. Profile generated scenes under peak load; reject content that drops below the framerate floor. Physics constants are a frozen contract a generator cannot vary.

---

# Sub-domain B — Input & Response (Forgiveness)

> **Forgiveness shrinks the gap between intent and response — it does not lower difficulty.** ✅ *Celeste* ships ≥7 simultaneous forgiveness systems and is still one of the hardest precision platformers made. These are latency compression, not assist.

## B.1 Buffer inputs so early presses fire on the first valid frame

Store an input for a short window (~100–200 ms / 6–12 frames @60fps) so a press during recovery executes when it becomes legal instead of being dropped. ✅ (Fighting-game motion buffers; *Celeste* jump buffering. Hit-stop *extends* the buffer/cancel window — Wagar.)

- **Test for:** earliest acceptable input per action is buffered ≥100 ms; buffered inputs don't fire stale intent (no accidental double-jump).
- **Failure modes — Input dropped** (early press → nothing; feels unresponsive at a true 60fps) and **over-buffered stale inputs** (500 ms buffer fires old intent → "ghost actions"). Keep the window ~100–200 ms.

## B.2 Add coyote time and edge-condition leniency

Allow the jump (or counter, or "on-track" state) for ~4–6 frames after the triggering condition lapses, because human reaction trails one frame. ✅ (*Celeste* coyote time = 5 frames / 83 ms; invisible — players read it as *accurate*, not generous.)

- **General form:** the simulation's boundary conditions (ledge, track edge, valid-state) should trail the input window by a perceptually invisible amount. Generalizes to fighting (post-block counter window), racing (corner-cut grace).
- **Test for:** tap 1 frame after the condition lapses → no action; tap inside the window → action fires. Parameterize per game.
- **Failure mode — No coyote time:** players "miss" jumps they visually made; reads as broken even when frame-accurate.

## B.3 Put a small forgiveness window on every high-frequency action

Jump, attack, dodge, interact — each benefits from a 2–8 frame edge in the player's favor (corner correction, apex half-gravity, momentum storage, stamina grace). Widen invisibly; document each window explicitly. ✅ (*Celeste*'s seven systems.)

- **Test for:** a "forgiveness off" debug mode reveals where the windows do work; tune so the resulting spike is deliberate, not an oversight. Test windows in the *hardest* level (over-generous corner correction can bypass tight puzzles).
- **Failure mode — Forgiveness creating unintended shortcuts:** generosity trivializes intended challenge.

**→ Procedural / headless implication.** Forgiveness is a **character-controller contract, centralized — never per-level.** A generated level that needs *tighter-than-designed* forgiveness to complete is malformed; the validator must reject it. The generator varies layout; it never touches the windows.

---

# Sub-domain C — The Juice Toolkit

> Each primitive communicates a *specific* type of information; the toolkit is a multi-channel, partly-redundant communication system, not additive decoration. The Kao inverted-U applies to the **summed** load per scene.

## C.1 Squash & stretch — read physics through shape

Deform along motion (stretch accelerating, squash on impact) while preserving apparent volume. ✅ (Thomas & Johnston: "volume remains constant." *Hollow Knight*: 1–3 px on a small sprite — readable, hitbox-safe. *Cuphead*: dramatic, valid as set genre expectation.)

- **Test for:** primary objects visibly deform on high-velocity transitions (takeoff/peak/landing/hit); apparent volume stays constant.
- **Failure mode — Rigid feel:** physics boxes with art painted on; landings look like teleports to a stop.
- **Genre note:** turn-based / VN / menu games apply squash to UI elements and portraits — a button that squishes on press says "input received" with no avatar.

## C.2 Anticipation & C.3 follow-through — the asymmetry that defines game animation

**Anticipation inverts meaning between enemies and the player.** On enemies, a wind-up = a fair, readable warning. On the **player avatar**, a wind-up = input lag. ✅ (Multiple game-anim educators; *Dark Souls* enemy wind-ups scale with damage; *Sekiro* player attacks have minimal startup, weight sold by follow-through.) This is the single biggest departure from the Disney principles and the bridge to `combat-design`'s telegraphing.

- **Follow-through:** secondary elements (hair, cape, weapon, VFX trail) keep moving 2–5 frames after the primary action resolves — weight *without* extending recovery (*Hollow Knight* slash VFX outlasts the recovery so control returns fast).
- **Test for (enemy):** every above-median-damage attack has a distinct anticipation ≥ player reaction floor (~150–200 ms); a new player can respond on first sight. **(player):** attack startup ≤ a responsiveness ceiling (~4–8 frames).
- **Failure mode:** enemy with no wind-up = unfair; player with long wind-up = sluggish controls.

## C.4 Hit-stop / freeze frames — the highest-impact single primitive

On impact, freeze the affected entities briefly (3–10 frames / 50–167 ms), duration scaling with magnitude; freeze *both* attacker and target. ✅ (Sakurai, Famitsu #490: duration scales with damage, tuned per move. *SFII* ~10 frames also extends the cancel window. Lin et al. 2022 ranks hit-stop a top-3 impact feature. *Dark Souls II* = the weak-feel cautionary case.)

- **Test for:** impacts above a force threshold trigger ≥3 frames; durations live in a per-event **table**, not computed live; cumulative hit-stop per second is capped.
- **Failure modes — No hit-stop** (hits feel like slapping air) and **stacked hit-stop** on rapid combos (controls feel laggy).

## C.5 Screen shake / camera kick — trauma-based, not per-hit reset

Offset the camera briefly on significant impacts. Use a **trauma** accumulator: `shake = trauma²` (small trauma near-invisible, large dramatic), trauma decays at a fixed rate. **In 3D, rotate rather than translate** (translation clips the camera through walls). ✅ (Nijman's "Art of Screenshake" — shake on gunfire was the single highest-impact tweak; Borderline Games trauma model; Lin et al. top-3.)

- **Test for:** a significant impact = ≥2 frames of visible displacement that decays smoothly; in 3D, no wall-clipping at max intensity; rapid impacts add trauma (no reset pulse).
- **Failure modes — No shake** (impacts feel distant) · **per-hit reset** (unnatural pulsing) · **3D translation clip-through**.
- **Genre note:** strategy/puzzle use shake sparingly (base destroyed, big combo); UI shake can signal an invalid play. Same model, calibrated low.

## C.6 Particles — distinct signature per event, hard budget

Short-lived bursts on impacts, state changes, and rewards; communicate event *type* (color/shape) and *magnitude* (count/size). Always budget — particles are the #1 source of juice-driven frame drops. ✅ (Jonasson & Purho "Juice It or Lose It"; *Peggle*'s once-per-level "Extreme Fever".)

- **Test for:** each event type has a distinct, non-colliding particle signature; per-scene caps on concurrent emitters / particles-per-emitter / screen coverage; density scales monotonically with importance.
- **Failure modes — Undifferentiated particle soup** (all events emit the same white splat) · **performance-busting storms** (uncapped emitters push frame time over 16.7 ms — breaks feel via latency).

## C.7 Tweening / easing — never animate a value linearly

Ease all non-instant value changes. Linear reads as robotic. ✅ (Penner's easing equations are the industry standard.) Ease-out for arrivals (panels settling, landings), ease-in for departures (launches), ease-in-out for cameras, overshoot/spring for reward confirmations.

- **UI timing:** hover/press 100–200 ms; transitions 200–400 ms; reveals 300–500 ms; >500 ms routine = sluggish.
- **Test for:** no value transition (camera lerp, health drain, score tally, spawn) uses constant velocity; each transition type has a documented easing curve.
- **Failure mode — All-linear transitions:** robotic health bars, nauseating snap-cameras, score tickers that read identically for 1 vs 1000.

## C.8 Sound layering — one immediate response, varied on repeat

Every action with a physical outcome needs ≥1 immediate audio response; important actions get layered audio (low body / mid texture / high sizzle) with pitch/volume variation to prevent fatigue. ✅ (Lin et al.: "sound coherence" top-3; *Nuclear Throne* composited, pitch-varied gunfire; *SMB* coin chime never fatigues.)

- **Test for:** mute-all → game still playable (sound augments, not substitutes); sound-only → every event identifiable by audio signature; fire one action 20× → does it stay non-fatiguing? Audio-visual sync within ~50 ms.
- **Failure modes — One fixed sample per event** (habituates to silence after ~10 repeats) · **audio-visual desync** (breaks the illusion of causality).

## C.9 The inverted-U caution — a per-action feedback budget with a hard ceiling

The summed feedback load per moment must stay in the Medium–High band. ✅ (Kao 2020 N=3,018: Medium & High beat both None and Extreme on play time, experience, motivation, *and* performance. Kao CHI 2024 N=1,699: over-amplification backfires by impairing the sense of agency.)

```
per-event feedback budget = {
  simultaneous_particle_emitters: 3–5 max,
  peak_shake_amplitude_px: minor 2 · major 8 · rare/climactic 20,
  hitstop_frames: 0 (weak) – 10 (rare climactic),
  simultaneous_sfx_layers: 3 max (low/mid/high),
  duration_until_screen_clear: 0.5–1.5 s
}
```

- **Rarity ladder:** reserve the *top tier* of each primitive (max shake, max hit-stop, slow-mo) for ≤once-per-session events (boss kill, run complete, rare loot). Every extra use at that tier habituates it away.
- **Test for:** a debug overlay sums live feedback load (particles, shake, audio voices); trigger every event type at once and confirm the peak stays under the High ceiling.
- **Failure mode — Uncapped juice escalation:** "more effects for big moments" with no ceiling produces Extreme-tier climaxes, which Kao shows are *worse* than Medium.

**→ Procedural / headless implication.** Juice lives in a **hand-authored, version-controlled feedback table** keyed by (event type × magnitude) with min/max guardrails set to Kao's Medium–High band. The generator *selects and composes* from the table — it must not invent new intensities. Ship a **juice-budget linter** that rejects any scene exceeding the ceiling; run headless telemetry (play time, quit-rate, FPS under peak juice) as proxies for Kao's measures.

---

# Sub-domain D — The 12 Animation Principles, Applied

The Disney 12 (Thomas & Johnston, *The Illusion of Life*) apply to real-time interactive systems with game-specific adjustments. The load-bearing ones for feel:

| # | Principle | Game application | Failure mode |
|---|-----------|-----------------|--------------|
| 1 | Squash & stretch | Deform on velocity changes; preserve hitbox volume; 1–3 px on small sprites | Rigid/weightless; sprite-hitbox desync |
| 2 | **Anticipation (inverted)** | Enemy wind-up = warning (good); player wind-up = input lag (bad) | No enemy tell = unfair; long player startup = sluggish |
| 5 | Follow-through / overlapping | Secondary elements continue 2–5 frames after the primary stops | Everything stops at once → plasticky |
| 6 | Slow in / slow out | Ease arrivals & departures; linear is almost never right | Robotic, dramaless transitions |
| 7 | Arcs | Jumps, swings, throws follow arcs unless deliberately mechanical | Straight-line motion feels robotic |
| 9 | Timing | Frame count sets pace; keep **consistent per attack type** so it's learnable | Random ±2–4 frame variance reads as buggy; mastery impossible |
| 10 | Exaggeration | Amplify past realism for clarity; scale to genre tone | Hyper-real accuracy reads as weak/dull |
| 11 | Solid drawing | Consistent readable silhouette across all states | Silhouette-destroying poses break targeting (→ `art-direction-and-readability`) |
| 12 | Appeal | Coherent, internally consistent characters | Averaged/generic procgen characters lose individual appeal |

**→ Procedural / headless implication.** Author animation states as a fixed library of `(anticipation, active, recovery)` triplets per action type; generators *select* from it, never compute new timings. Principle 9 demands the same attack type share one timing signature across all generated enemies.

---

# Sub-domain E — Camera Feel

The camera is a feedback device, not just a framing tool: lag/soft-follow conveys momentum, kick conveys force, lookahead conveys agency. ✅ (*Super Mario World* directional lookahead + soft-follow; Nijman & Lin et al. on camera kick.)

- **Test for:** the camera doesn't move in pixel-exact lockstep with the avatar (unless deliberately screen-locked); idle↔moving↔stop transitions are eased, not snapped; impacts produce a smoothly-decaying offset.
- **Failure modes — Locked camera with no give** (moving a cursor on a painting, not inhabiting a body) · **excessive lag** (avatar outruns the view).
- **Platform techniques:** deadzone (anti-jitter; too large → avatar lost at edges), lookahead (too aggressive → races ahead of commitment), edge/platform snapping (instant → jarring), boss-lock (too large arena → removes spatial agency). 3D shake = rotation, not translation (E ↔ C.5).

---

# Sub-domain F — UI / Menu Feel

Every interactive UI element benefits from juice; the same rarity ladder applies to celebration moments. ✅ (*Peggle* Extreme Fever as a UI moment; *Bejeweled/Candy Crush* cascade juice on pure UI; Juicy-UI practice.)

- **Buttons:** hover (scale 1.02–1.05× / glow, 100–200 ms) → press (squish to 0.95×, <100 ms) → audio click; navigation, confirm, and back each get a *distinct* audio signature.
- **Transitions:** eased fade/slide/scale 200–500 ms (faster = energetic, slower = ceremonial); flag <100 ms (glitch) or >600 ms routine (feels like loading).
- **Celebration:** reserve the most spectacular effect for the rarest event; fire the top tier ≤once per session.
- **Test for:** navigate the entire menu **eyes closed** — can you tell what's highlighted, when a selection confirms, when you go back? If not, audio feedback is missing.
- **Failure modes — Silent menus** (players feel blind; accessibility suffers) · **instant state cuts** (breaks continuity) · **flat reward screens** (routine and perfect clears feel identical).

**→ Procedural / headless implication.** UI juice is centralizable: a generated game draws from a shared component library (button/panel/transition/reward) whose feel params are hand-tuned once and reused. The generator composes from the library; it never invents UI variants — so all generated games feel polished at the UI layer regardless of content quality.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Floaty/unresponsive controls | Latency past threshold; cause-effect breaks | Measure & cut input-to-pixel latency (A.1) |
| Polish masking bad feel | Effects hide lag / inconsistent physics | Strip effects; fix simulation first (A.3) |
| Zone-to-zone float | Learned jump arcs go invalid | Physics constants are version-controlled (A.2) |
| Input dropped / over-buffered | Early press lost, or stale ghost actions | ~100–200 ms buffer (B.1) |
| No coyote time | "Missed" jumps the player visually made | 4–6 frame ledge leniency (B.2) |
| Rigid feel | Objects look like physics boxes | Squash/stretch on velocity changes (C.1) |
| Sluggish avatar | Long player wind-ups read as lag | Minimize player startup; weight via follow-through (C.2/3) |
| No / stacked hit-stop | Hits feel weak, or controls feel laggy | Bounded per-event hit-stop, capped/sec (C.4) |
| Per-hit reset shake | Unnatural pulsing | Trauma accumulator + decay (C.5) |
| Particle soup / storms | Unreadable events / FPS drops | Distinct signature + hard caps (C.6) |
| All-linear transitions | Robotic bars, snap cameras | Ease every value change (C.7) |
| Fixed-sample sound | Habituates to silence after ~10 repeats | Layer + pitch-vary; sync ≤50 ms (C.8) |
| Uncapped juice escalation | Extreme-tier climaxes | Feedback budget + rarity ladder (C.9) |
| Locked camera with no give | Cursor-on-a-painting motion | Soft-follow + lookahead + eased transitions (E) |
| Silent menus | Can't navigate eyes-closed | Multimodal UI feedback (F) |

---

## Caveats

- **Strict vs. popular "game feel."** Wagar (2020 ⚠️) is right that many popular talks (incl. Nijman's "30 tweaks") demonstrate *design* changes (enemy HP, fire rate) not *feel* changes. This skill separates feel (latency, forgiveness, physics) from juice (polish) from design (mechanics) — fix in that order.
- **Kao generalizability.** N=3,018 on one action-RPG; the inverted-U direction is robust (CHI 2024 + practitioner reports), but the specific Medium/High/Extreme breakpoints don't transfer across visual vocabularies (a bullet-hell ≠ a text RPG). Kao constrains the ceiling; the exact ceiling needs per-title playtest telemetry.
- **Disney principles were built for film.** Timing is dynamic, follow-through competes with player-controlled recovery, and anticipation inverts on player characters. All applications here are adapted, not direct.
- **Latency is interaction-type, not just genre.** In an RTS, selecting a unit needs ~100 ms feel while a move order tolerates ~500 ms. Genre thresholds are starting points; validate per-interaction.
- **The feedback table must be hand-authored.** It's the one artifact that can't itself be generated without circular dependency — a designer (or deeply-constrained, human-validated AI) authors and playtests it before any generated content is evaluated against it.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
