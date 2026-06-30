# Game Feel & Juice — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for the feel of any action. Run against a feel-tuning pass, a controller spec, or a batch of generated content. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** control → simulation → forgiveness → juice. Don't juice a verb that doesn't yet feel right.

---

## A · Control & simulation (Swink's foundation)

**Do**
- [ ] Hit the genre's **latency budget** (≤50 ms input-to-pixel for action @60fps; RTS tolerates ~500 ms) — measured on target hardware, not estimated.
- [ ] Keep avatar **physics as version-controlled constants**; signpost any intentional change (low-gravity zone) before the player commits.
- [ ] Defer polish until the **stripped** simulation already feels acceptable.

**Don't**
- [ ] Don't ship a verb whose feel depends on effects to mask lag or inconsistent physics.
- [ ] Don't vary avatar physics per zone without a clear pre-commit cue.

**Test for** — Is measured input-to-pixel latency under the genre threshold? Mute sound + disable particles + strip screen effects: does the core still feel right?

---

## B · Input & forgiveness (compress intent→response, don't lower difficulty)

**Do**
- [ ] **Buffer inputs** ~100–200 ms so an early press fires on the first valid frame.
- [ ] Add **coyote time** (~4–6 frames) and edge-condition leniency (counter window, corner correction).
- [ ] Centralize every forgiveness window in the **character controller**; document each one.

**Don't**
- [ ] Don't drop early presses (feels unresponsive at a true 60fps).
- [ ] Don't over-buffer (>~200 ms) and fire stale "ghost" inputs.
- [ ] Don't let generous corner correction trivialize a tight intended challenge.

**Test for** — Tap 1 frame after a ledge lapses → no jump; tap inside the window → jump fires. Does a "forgiveness off" debug mode reveal where the windows do real work? Do windows hold up in the *hardest* level, not just average ones?

---

## C · The juice toolkit (each channel communicates; budget the sum)

**Do**
- [ ] **Squash/stretch** on high-velocity transitions, preserving apparent volume (1–3 px on small sprites).
- [ ] Keep **enemy anticipation** prominent (warning) and **player anticipation** minimal (else input lag); sell player weight via **follow-through**.
- [ ] Fire **hit-stop** from a per-event table (3–10 frames, scaled to magnitude); cap cumulative hit-stop/sec.
- [ ] Use **trauma-based screen shake** (accumulate + decay); in 3D **rotate**, don't translate.
- [ ] Give each event a **distinct particle signature**; cap concurrent emitters / particles / screen coverage.
- [ ] **Ease every value change** (camera, health, score, spawn, UI) — no linear interpolation.
- [ ] Give every physical action **≥1 immediate sound**; layer + pitch-vary important ones; sync A/V within ~50 ms.

**Don't**
- [ ] Don't leave actions rigid (no squash) or dead-stopping (no follow-through).
- [ ] Don't ship hits with no hit-stop (floaty) or stacked hit-stop on rapid combos (laggy).
- [ ] Don't reset shake per hit (pulsing) or translate the 3D camera into walls.
- [ ] Don't emit the same splat for every event (particle soup) or leave emitters uncapped (FPS-killing storms).
- [ ] Don't fire one fixed-pitch sample per repeated action (habituates to silence).

**Test for** — Does each event type have a distinct, non-colliding signature across particles + sound + shape? Is audio identifiable with the screen off? Does any uncapped effect drop FPS under peak load?

---

## C.9 · The feedback budget (the inverted-U ceiling)

**Do**
- [ ] Keep summed feedback in the **Medium–High band** (Kao 2020) — a measurable per-event budget (emitters, peak shake px, hit-stop/sec, sfx layers).
- [ ] Build a **rarity ladder**; reserve the top tier of each primitive (max shake/hit-stop, slow-mo) for ≤once-per-session events.

**Don't**
- [ ] Don't maximize juice — *Extreme* hurts experience, motivation, *and* performance as much as *None*.
- [ ] Don't spend peak effects on routine actions (spectacle inflation leaves nothing for climaxes).

**Test for** — A debug overlay sums live feedback load: trigger every event type at once — does the peak stay under the High ceiling?

---

## D–F · Animation, camera, UI feel

**Do**
- [ ] Keep **timing consistent per action type** (Principle 9) so the rhythm is learnable; preserve a readable **silhouette** across all states.
- [ ] Give the camera **give**: soft-follow, lookahead, eased idle↔move↔stop, smoothly-decaying impact offset.
- [ ] Juice UI: hover (100–200 ms) → press squish (<100 ms) → audio; distinct click for navigate / confirm / back; eased 200–500 ms transitions.

**Don't**
- [ ] Don't randomize per-attack timing (±2–4 frames reads as buggy; blocks mastery).
- [ ] Don't lock the camera in pixel-lockstep (cursor-on-a-painting) or let it lag so far the avatar outruns it.
- [ ] Don't ship silent menus or instant state cuts.

**Test for** — Navigate the entire menu **eyes closed**: can you tell what's highlighted, when a selection confirms, and when you go back?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Physics constants + forgiveness windows** (centralized in the controller) → **physics-contract validator** (reject levels needing tighter-than-designed windows or that clip the jump arc).
- [ ] **Per-action feedback table** (event × magnitude → bounded bundle, capped to Medium–High) → **juice-budget linter**.
- [ ] **Animation library** of `(anticipation, active, recovery)` triplets with one timing signature per action type → **animation-state validator** (no generator-computed timings).
- [ ] **Shared UI component library** (hand-tuned feel) → generator composes, never invents UI variants.
- [ ] **Frame-time floor** under peak generated load → **performance validator** (reject scenes that drop below the 60fps floor).

> For the **combat-specific** application of these tools — hit-stop per damage tier, telegraphing as fairness, enemy/encounter feel — apply `combat-design` on top of this skill. Gate every generated batch through `procgen-review` as well.
