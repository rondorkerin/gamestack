# Pacing & the Player Journey — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for pacing at every timescale. Run against a level review, a session-design pass, a retention-loop audit, or a batch of generated content. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** establish the macro interest curve → design the rest rhythm → schedule novelty → wire the loops → spec the first hour → implement the pacing director. A pacing director applied to content with no planned arc is just an enemy spawner.

---

## A · The interest curve (macro arc)

**Do**
- [ ] Author a **playthrough-scale intensity graph** (X = % complete, Y = 0–10) before content production. Hook ≥7/10 in first 5 minutes; target peak in the 85–95% band; denouement in the final 5%.
- [ ] Place the three highest-intensity set-pieces at approximately **40%, 70%, and 90%** of the playthrough.
- [ ] Verify the fractal rule at **all four timescales**: playthrough, session (20–90 min), level, and encounter each have a distinct hook → rising interest → peak → rest shape.
- [ ] Open with an immediately sensory hook — the player identifies what the game feels like **within 2 minutes** without reading UI text.
- [ ] Follow every climax with **2–5 minutes of lower-pressure, emotionally resonant content** (epilogue, credits walk) before the hard end.

**Don't**
- [ ] Don't open with unskippable exposition, character creation, or a tutorial corridor before any interesting decision.
- [ ] Don't let the final encounter register lower emotional intensity than a mid-game set-piece.
- [ ] Don't design only one timescale (e.g., great encounter pacing but flat session pacing loses players between sessions).

**Test for** — Graph intensity across a playtester session: are there visible peaks and valleys at each timescale? Does the final third of the game contain the highest-intensity sequence? After the final moment, does the game allow 2–5 min of lower-pressure content?

---

## B · Challenge & rest rhythm

**Do**
- [ ] After every high-intensity peak (boss, set-piece, major revelation), guarantee **at least one deliberate low-intensity beat** before the next escalation.
- [ ] Keep the ratio of high:low beats to **≤3:1 by duration**.
- [ ] Use the rest-beat toolbox: exploration/travel, NPC dialogue scenes, inventory management, lore discovery, vista/environmental payoff.
- [ ] Ensure rest beats carry **latent tension** — ambient audio, dread signals, or knowledge that the next challenge is close.

**Don't**
- [ ] Don't let any run of high-intensity beats exceed **10 minutes without a deliberate low-intensity beat**.
- [ ] Don't design a rest beat that convincingly signals "all danger is permanently past" (False Safety).
- [ ] Don't use combat pause as the only rest-beat type — varied downtime keeps players from fatiguing to a specific beat shape.

**Test for** — Map every beat in a 30-minute sequence. Does any continuous high-intensity run exceed 10 min? Do playtesters in rest beats visibly relax but remain alert? If they fully disengage (check phones, leave), the rest beat carries no tension thread.

---

## C · Novelty pacing and the mid-game sag

**Do**
- [ ] Schedule **at least one new mechanic, system, or content type every 20–40 minutes** of designed play.
- [ ] Introduce each new mechanic in the teach-test-twist sequence: (1) safe context with no failure stakes, (2) direct challenge application, (3) unexpected variation.
- [ ] Ensure **all core mechanics are introduced by the 80% mark**. Any mechanic not introduced by then must be reclassified as a power escalation of an existing one or cut.
- [ ] Pair every new content zone with at least one **mechanical difference** from the previous zone.

**Don't**
- [ ] Don't introduce a genuinely new system in the **final 15–20% of the game** — it produces a late tutorial with no mastery payoff.
- [ ] Don't count environmental reskins (new biome art, new faction art) as mechanical novelty — expect the interest hit to expire in ~10 minutes.
- [ ] Don't front-load all mechanics in the tutorial, leaving a mechanic-stagnant middle.

**Test for** — List every distinct mechanic/system; chart introduction timing. Is there any gap >40 minutes without a new introduction? Are all new systems introduced by the 80% mark? For each new zone: list its mechanical differences from the prior zone.

---

## D · Nested loops and retention ethics

**Do**
- [ ] Design a distinct payoff type for **each of the four loop scales**: core (< 1 min), encounter (2–20 min), session (20–90 min), lifetime (days+).
- [ ] Layer **at least one arc** (one-time narrative/emotional payoff) over every cluster of gameplay loops.
- [ ] Ensure a 20-minute play session that ends in failure still delivers **at least one arc payoff** (story beat, new dialogue, permanent unlock hint).
- [ ] Design appointment mechanics to **reward attendance positively**: missing days costs nothing; showing up earns a bonus.
- [ ] Run the ethics test on every appointment mechanic: does a player who misses 3 days **lose concrete progress** (dark pattern) or merely **fail to gain a bonus** (acceptable)?

**Don't**
- [ ] Don't ship a loop with no corresponding arc — skilled-but-disengaged players stop returning.
- [ ] Don't use **loss aversion** as the mechanism for return: no expiring progress, no decaying ranks, no punished absence.
- [ ] Don't ship **variable-ratio real-money purchase mechanics** without legal review — Belgium (2018), Netherlands (2018) have criminalized paid loot boxes; FTC fined Epic $245 M (2022).
- [ ] Don't obscure the real cost of continued play through premium currency obfuscation.

**Test for** — After a failed 20-minute session: did the player receive at least one arc payoff? Can you name a distinct payoff type for each of the four loop scales? For every appointment mechanic: if the player misses 3 consecutive days, what specifically do they lose?

---

## E · First-hour pacing

**Do**
- [ ] Deliver the player's **first meaningful action before the first significant UI explanation**. Let the mechanic speak before the manual.
- [ ] Match the first-hour arc to the genre (see `GUIDE.md` §E.2 genre table).
- [ ] For roguelites: confirm the meta-loop exists immediately after the first death. For open-world RPGs: ensure the "scope reveal" moment is reached before the prologue overstays.
- [ ] Time first input to first satisfying output: **under 2 minutes for action/FPS; under 5 for most other genres**.

**Don't**
- [ ] Don't open with an unskippable tutorial exceeding **5 minutes** before any meaningful play.
- [ ] Don't apply an FPS blitz opening to a strategy game, or a strategy game's slow build to an action game (genre mismatch pacing).
- [ ] Don't require character creation or backstory decisions before the player has any desire to learn.

**Test for** — What is the player's first input? What is the first satisfying output of that input? How many minutes apart are they? In the first 5 minutes, does the player perform the core action at least once before reading a tooltip about it?

---

## F · The pacing director (generated content)

**Do**
- [ ] Implement the pacing director as the **content generator's controller**, not a post-hoc overlay. Generated encounters are *requested by the director* with a target intensity tag; the generator satisfies the request.
- [ ] Track **intensity_score per player** (damage taken + enemies killed nearby − time_since_combat × decay); drive all spawning and content requests off this score.
- [ ] Enforce the four-phase cycle: **Build Up → Peak → Relax → Dead Time**. After a Relax phase, hold Dead Time before restarting Build Up.
- [ ] Apply the **first-hour protected mode**: hand-authored hook → controlled intro encounter (intensity ≤ 0.5) → guaranteed arc payoff within 20 min.
- [ ] Use **distance-to-objective** as the macro difficulty overlay — difficulty scales with approach to the objective, not time elapsed.
- [ ] Pre-author a **novelty palette** (mechanic A, B, C…) with target introduction thresholds; the director schedules introduction, never the generator alone.
- [ ] Track recency of each content-type introduction; enforce a **cooling period** before a type can be re-introduced as novel.

**Don't**
- [ ] Don't let the director run a continuous high-intensity sequence exceeding **10 minutes without enforcing a Relax phase**.
- [ ] Don't let the generator introduce mechanics the director has not scheduled — novelty is authored, not emergent.
- [ ] Don't use time-based pacing as the only axis in generated content — fast and slow players both deserve the same arc.
- [ ] Don't skip the first-hour protected mode for generated content — the first generated encounter that can kill the player before they understand the run structure is a guaranteed dropout.

**Test for** — Instrument five playthroughs: does intensity_score stay above 0.7 continuously for >10 minutes (no Relax enforced)? Does it stay below 0.85 for >5 continuous minutes during a sequence designed as Build Up (Director not building)? In a 20-minute open-world free-roam: is there at least one high, medium, and low intensity beat? Any 10-minute mono-intensity stretch?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Macro interest curve graph** (authored at pre-production) → **curve-shape validator**: generated session must contain at least one peak above 0.8 intensity, one below 0.3, and must end with a peak in the 85–95% band.
- [ ] **Novelty palette + introduction schedule** (mechanic list + target-time thresholds + cooling periods) → **novelty-schedule linter**: no gap >40 min; no mechanic introduced after 80% mark; no content type re-introduced before its cooling period expires.
- [ ] **Four-loop payoff map** (named payoff type per loop scale) → **loop-completeness check**: any generated session must deliver at least one encounter-level and one session-level payoff before the player quits.
- [ ] **Ethics audit table** (list of all appointment mechanics + classification: gain-reward vs. loss-punish) → **ethics validator**: block any mechanic classified as loss-punish before legal review.
- [ ] **Pacing director phase log** (per-session export of phase transitions and intensity_score) → **flat-line detector**: flag any 10-min window with intensity_score variance < 0.1 (monotonic intensity — same failure at micro or macro scale).

> For procedurally generated encounter sequences, apply `combat-design` for encounter-level intensity targets, and gate every generated batch through `procgen-review`. For spatial pacing in open worlds, apply `open-world-design` landmark-density rules as input to the pacing director's zone-density map.
