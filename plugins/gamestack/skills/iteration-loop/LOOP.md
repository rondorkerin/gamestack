# Iteration Loop — full procedure

One loop shape — **Reference → Diff → Prioritize → Generate/Implement → Verify** — instantiated per modality. See `SKILL.md` for the five-step summary and scope. This file covers the parts that don't fit in a summary: the per-modality table, the preview-harness pattern, why animations need a different capture method than a screenshot, the playtester channels, and the escalation pattern for when generation is the wrong tool.

---

## 1. The loop is generic; only these four things change per modality

| | Reference | Preview harness | Diff method | Verify via |
|---|---|---|---|---|
| **Visual** (terrain, biome, dungeon, town, castle, character, enemy, NPC, spell-fx, …) | Concept art / mood boards / reference plates, organized so a given system can pull just its own subset | A bootable, screenshot-able rig that renders *one system in isolation* on demand (§2) | Side-by-side eyeball: name the single biggest delta — palette, silhouette density, lighting mood, a missing asset type. Animated subjects need §3, not a single screenshot. | `art-direction-and-readability` (is the target even readability, or literal match?), `3d-graphics-and-rendering` / `shaders-and-vfx` / `animation-systems` / `procedural-geometry` (what actually gets tuned) |
| **Systemic** (balance, economy, difficulty, progression) | The design doc's stated target: no dominant strategy, a win-rate band, a faucet/sink ratio | A simulation or self-play harness, or a metrics dashboard over real play data | Compare measured numbers against the stated target band, not vibes | `difficulty-and-balancing`'s simulation/self-play method |
| **Narrative** (lore, quests, history) | The lore bible's never-violate fact set / established canon | A fact-checking pass: new content vs. the canon grid | Contradiction or drift detection against already-established facts, not freeform re-reading | `worldbuilding-and-lore` (the fact-check grid), `ai-authored-content-coherence` (generate-then-rationalize, the self-review pass) |
| **Content variety** (any modality, a *different* axis from the above three) | Prior committed instances of the same type | N/A — the committed corpus is the reference | Cross-instance sameness scan (oatmeal test) | `procgen-review` + `shared/GATE.md` |

Fidelity (does it match the target?) and variety (is each instance distinct?) are independent axes — a batch can be high-fidelity and oatmeal-sameish at once, or vice versa. Run both gates; don't substitute one for the other.

**Adding a fifth modality** (audio is the obvious next one, currently uncovered by any gamestack skill): fill in the same four columns. The loop shape doesn't change — only what "reference," "preview harness," "diff," and "verify" mean for that modality.

---

## 2. The preview-harness pattern (visual modality)

Every visual system needs a way to be **booted in isolation and captured on demand** — what some projects call a "bench": a rig that loads just the terrain generator, just the dungeon generator, just one character rig, etc., without needing the full game running. Properties that make a preview harness usable in this loop:

- **Boots one system at a time**, parameterized (which biome variant, which dungeon seed, which character/animation).
- **Captures deterministically on command** — a screenshot or short capture sequence, not a human standing in the editor.
- **Outputs to a stable path** so Diff can compare this run against the last one, and against the reference.

If a system has no preview harness yet, building a minimal one is the first loop cycle for that system — Diff can't run against a moving target you can't freeze and screenshot.

---

## 3. Animations can't be eyeballed from one frame — capture a contact sheet

A single screenshot of an animated subject tells you almost nothing about whether the *motion* is right — only the pose at one instant. The fix is mechanical, not judgment-based: extend the preview harness's capture step to produce a **multi-frame contact sheet** per clip instead of one frame.

- For a looping clip: capture **start / loop-mid / end** as three panels in one image.
- For a one-shot (attack, death, pickup): capture **first / mid / last** frames the same way.
- Lay them out left-to-right in a single image so the Diff step reads one flipbook-style artifact, not a folder of loose frames.

This turns "I can't eyeball animation" into "read a 3-panel strip" — comparable in cost to the single-screenshot diff used for static visuals. See `animation-systems` for what's actually being judged in each panel (anticipation, follow-through, foot placement, blend pops).

---

## 4. The two playtester channels

Inspection (§1's per-modality diff) checks one system in isolation. Playtesting checks systems **in combination**, in actual play — the layer where a biome that looks right alone reads wrong next to its neighbor, or a balance change that simulates fine breaks under real player behavior. Run both channels; they catch different things.

### Human playtester

The only source of genuine fun/feel judgment. Route observed signals to the phase that owns the fix — `game-design-process` Phase 5 has a starter signal→cause→route table; extend it per-project as new signal types show up.

For the session itself — objectives, internal/external/focused session types, observation guides, note-taking templates, post-session analysis — don't build this from scratch. **[BMad Game Dev Studio](https://github.com/bmad-code-org/bmad-module-game-dev-studio)**'s `gds-playtest-plan` workflow (third-party, MIT) is a structured human-playtest-session-design tool built for exactly this; its sibling `gametest` workflows (`gds-test-design`, `gds-test-automate`, `gds-performance-test`, `gds-e2e-scaffold`) cover automated/perf testing alongside it. Pair it in (`/plugin install bmad-game-dev-studio@gamestack`) rather than reinventing session design here — see `docs/architecture.md` for the full relationship between the two packs.

### AI playtester

An agent that plays or drives the game (or a single scene) and **self-verifies** without a human in the seat, through a **playtester API** — programmatic access to:
- **Logs** — game-state events, errors, the same telemetry a human playtester's session would emit.
- **Screenshots** — captured the same way the preview harness does for §2/§3, but during live play rather than an isolated rig.
- **Input injection**, where available — lets the agent actually act instead of only observing a human's session.

What this buys: an AI playtester can run every loop cycle (cheap, deterministic, no scheduling a human), closing the Verify step for objective signals (does the player ever reach this room, does this enemy's telegraph register before the hit lands, does the framerate hold in this scene). **Escalate to a human playtester when the read is ambiguous or the question is inherently subjective** ("is this fun," "does this twist land") — not just because human playtesting is slower to arrange. An AI playtester with no log/screenshot access is just a script; the playtester API is what makes it a playtester rather than a fuzzer.

If a project has no playtester API yet, exposing one (structured logs + on-demand screenshot capture, minimally) is worth building before leaning on AI playtesting — without it, every AI-driven cycle reduces to inspection (§1), not play.

---

## 5. External request escalation — when generation is the wrong tool

The Generate/Implement step defaults to generation (asset-gen pipeline, procedural systems, AI-authored content within canon) because that's what scales. It is not always the right call — a hand-authored hero asset, a licensed/voice-acted line, a specific reference the team wants pixel-matched, or anything where generation would visibly underperform a sourced or commissioned alternative.

Don't silently force a worse generated result to keep the loop moving. Instead:

1. Log a structured request: **what** is needed, **why** generation isn't the right tool here, and **which reference** it's closing the gap for (the same reference Diff named in step 2).
2. Park it in a human-curated backlog (whatever your project's append-only request/backlog convention is) rather than blocking the rest of the loop on it.
3. Move on to the next-highest-priority delta. The escalated item gets picked up on the human's own time, then re-enters the loop at Verify once it lands.

This keeps the loop from degrading into "generate something, anything" when the honest answer is "this one needs a human."

---

## 6. Running the loop in practice

1. Pick the system with the largest known gap (or the next one in production order).
2. Run one cycle: Reference → Diff (name one delta) → Prioritize (confirm it's the highest-benefit one) → Generate/Implement → Verify.
3. If the delta closed, move to the next-highest delta on the same system, or the next system.
4. If content was generated as part of Implement, gate it through `procgen-review` (variety axis) in addition to this loop's Verify (fidelity axis) before committing.
5. Periodically (not every cycle) run the playtesting channels (§4) across the whole game, not just the system just touched — combination effects only show up there.

This loop runs continuously during `game-design-process` Phase 3 (Content) on individual systems, and is the mechanism behind Phase 5 (Playtest & iterate) at the whole-game level.
