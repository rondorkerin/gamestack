# Permadeath, Lethality & Meta-Progression — Guide

Make death create stakes, not frustration. Pair with `CHECKLIST.md` for the actionable version.

> **The central law:** permadeath is the purest engine of "interesting decisions," but only if the game is **fair** — deaths must be the player's fault, telegraphed, and readable. If you generate lethality without generating *readability and information*, you produce frustration, not stakes. Build the fairness backbone first.

> **Reading the tags.** Distilled from a complete deep-research synthesis with its own caveats (carried in §5). It does not use per-claim ✅/⚠️/❌ tags. **Areas 1–3 rest on shipped games; all of Area 4 (single-save + long-form open world) is extrapolation requiring playtests** — flagged heavily throughout. AI-authoring moves marked **(extrapolation — test it)**.

---

## Area 1 — Permadeath demands fairness

Because every choice is irreversible, each becomes a "real" decision in Sid Meier's sense, and the one-life structure is what gives each room "actual stakes" (Kasavin). That weight is only *earned* if the player could always have done differently with the information available.

**Rule 1.1 — Make death the player's fault, never the dice's.** Generate threats so any death is attributable to a player decision they could have made differently with available information.
- *Exemplar:* Spelunky (Derek Yu). Jon Shafer's "Fairness, Discovery & Spelunky" (2013): arrow traps are always spottable, enemies have fixed exploitable patterns, "virtually everything that happens is within the player's control." Yu: the world "follows the same rules you do."
- *Test for:* every lethal hazard has ≥1 pre-damage signal (visual/audio) AND ≥1 counter-action reachable from the player's current state. No unseeable instant kills.
- *Failure mode:* **Unfair death / "fake difficulty"** — off-screen, unsignaled, or un-haveable-information deaths. Catastrophic under permadeath: it invalidates the whole run.

**Rule 1.2 — A decision is "interesting" only if outcomes are predictable enough to reason about.** Each meaningful choice needs (i) consequences, (ii) partially predictable outcomes, (iii) trade-offs.
- *Exemplar:* Sid Meier, "Interesting Decisions" (GDC 2012) — err on the side of giving the player *too much* information.
- *Test for:* per decision node, ≥2 options chosen across telemetry (no dominant option ≥~85% pick rate) AND the player had information to forecast outcomes.
- *Failure mode:* the **dominant-option trap** (one obviously-correct choice) and the **CYOA trap** (unpredictable outcomes = guesswork, intolerable under permadeath).

**Rule 1.3 — Telegraph every lethal action; budget the telegraph window to lethality.** Deadlier attacks get longer/clearer tells.
- *Exemplar:* Death's Gambit team, "Designing for Difficulty: Readability in ARPGs" — readability = telegraphing (before) + expectations (after), and it "arguably has the largest impact on frustration." (See `combat-design` for the full telegraphing craft.)
- *Test for:* every attack removing >X% HP has an anticipation animation/sound ≥ N frames; readability holds at **generated** camera distances and lighting (critical for procedural 3D dark interiors).
- *Failure mode:* the **1-frame uppercut** — unreadable instant attacks; in procedural 3D, depth-ambiguity that hides the tell.

**Rule 1.4 — Prefer deterministic or transparent randomness for lethal outcomes.** Where death can hinge on a roll, make the system deterministic-on-reload or so randomized that re-rolling is pointless; always surface the odds.
- *Exemplar:* XCOM: Enemy Unknown — Jake Solomon (TIME, 2012): "We use synchronous random in combat so the player can't just reload when they miss a shot." The RNG seed/index is stored in the save, so identical action sequences reproduce identical results. *Counterpoint:* FTL's game-ending encounters that feel like "the game slamming the door" — randomness must stay readable.
- *Test for:* no single unsurfaced roll can end a run without prior player-controllable mitigation; displayed probabilities match true probabilities (note XCOM: Enemy Within adds a hidden bad-streak breaker on Easy/Normal only).
- *Failure mode:* the **95%-that-missed** — surfacing a probability then violating expectation, so death feels like a lie.

> **Procedural / AI implication.** The generator must treat **readability and information as first-class generation constraints, not decoration.** Hand-author a fairness backbone: per-tier telegraph budget, guaranteed escape/counter-options per encounter, lighting/LoS minimums, a hazard-density ceiling. Run an automated **solvability/survivability pass** on each generated encounter (can a competent agent survive with the information available?). Without this, AI-authored lethality degenerates into a random-death generator — the worst possible outcome here.

---

## Area 2 — Mitigations: cut the cost of failure, keep the stakes

**Rule 2.1 — Keep the failure→restart loop short relative to total content.** A death should cost minutes-to-an-hour of *re-traversable* progress, not the whole game.
- *Exemplar:* Kasavin (Inverse, 2021): "The part that's interesting about roguelikes is that it's different every time… To experience that, they've got to kill you. If you're playing for five hours before restarting, you're not experiencing the cool part." Hades/Spelunky/Dead Cells/FTL/Slay the Spire all use sub-hour runs.
- *Test for:* median run length is bounded; on death, cost is dominated by *replayable variety*, not *repeated identical content*.
- *Failure mode:* the **long-run-death cliff** — losing hours of unique progress to one death (the central risk for a long-form game; see Area 4).

**Rule 2.2 — Offer opt-in, judgment-free difficulty assistance that preserves the loop.** Reduce lethality gradually and reversibly without removing death-and-restart.
- *Exemplar:* Hades **God Mode** (Dec 2019) — the *Deus Ex Machina* effect: an immediately palpable **20% damage resistance, +2% per subsequent death, capping at 80%**, so a struggling player breaks through while still learning patterns. Kasavin: a plain easy mode "wouldn't cut it" because "if you could just blow through it, what's interesting… goes away." Named in the menu, "described without judgment."
- *Test for:* assistance is toggleable mid-game, eases on repeated failure, and does NOT disable death/meta-progression/stakes; default experience unchanged.
- *Failure mode:* the **binary easy mode** that deletes the core experience, or the **shame-gated toggle** buried/stigmatized so those who need it won't use it.

**Rule 2.3 — Pick one stance on save-scumming and enforce it consistently.** (i) single-save/delete-on-load, (ii) deterministic seed, (iii) hyper-randomization, (iv) consequence-delay.
- *Exemplar:* Roguelikes delete the save on load (Berlin Interpretation, Jeff Lait 2008: "the savefile is deleted upon loading"). XCOM = seed determinism. Diablo/PoE defeat scumming via overwhelming randomization; The Witcher delays consequences by hours; Fire Emblem: Three Houses' Divine Pulse makes rewind a *limited diegetic resource*.
- *Test for:* no trivial reload path reverses a death or re-rolls an outcome at zero cost. For single-save: exactly one slot, continuously overwritten, no manual-backup hook exposed.
- *Failure mode:* the **accidental scum-faucet** (save-anywhere + visible RNG) that mechanically invites reload-abuse; plus **crash-induced total loss** (the real risk of delete-on-load).

**Rule 2.4 — Shape difficulty as earned, transparent escalation.** Increase difficulty as a consequence of player success, ideally opt-in, never via hidden rubber-banding.
- *Exemplar:* Dead Cells **Boss Stem Cells** (5 BSCs raise enemy tier/density/variety and limit healing; from 2 BSC all health fountains break; the 5-BSC Malaise escalates and kills if unchecked, with higher rewards). Hades' Pact of Punishment / Heat does the same.
- *Test for:* difficulty increases are player-visible and tied to demonstrated mastery or explicit opt-in; reward scales with risk.
- *Failure mode:* **opaque rubber-banding** (players feel cheated) and **flat difficulty** (skilled players bored — Tarn Adams' inverse: "If you're bored, you win").

**Rule 2.5 — Make every death teach.** Guarantee each death yields actionable information OR durable progress.
- *Exemplar:* Kasavin: "how to take the sting of failure and reduce that as much as possible." Spelunky's main hurdle is *knowledge*, not execution; Dark Souls bloodstains let you (and others) learn from the exact death spot.
- *Test for:* post-death, the player gains ≥1 of {new threat knowledge, meta-currency, narrative beat, unlock}. No death returns to an identical state with nothing gained.
- *Failure mode:* the **pure-reset** (lose everything, learn nothing) — the demoralizing risk of pure roguelikes.

> **Procedural / AI implication.** Short runs generate easily; **teaching deaths do not** — the generator must guarantee each death is legible enough to learn from (loops back to Area 1's backbone). For God-Mode-style assist, expose a **single global lethality scalar** the generator respects rather than hand-tuning thousands of encounters. Implement deterministic RNG as **seeded, reproducible streams per subsystem** (combat / loot / world-gen) so one subsystem's reroll can't leak into another — essential in a single-save game where the seed *is* the anti-scum mechanism.

---

## Area 3 — Meta-progression: failure as progress, and diegetic death

**Rule 3.1 — State your roguelike↔roguelite position explicitly; it's defined by persistence across death.**
- *Exemplar:* Berlin Interpretation (2008) — Random Environment Generation + Permadeath head the "high-value factors"; canon = ADOM/Angband/Crawl/Nethack/Rogue. "Roguelites" (Hades, Dead Cells, Spelunky, Slay the Spire) keep permadeath + procgen but add persistent meta-progression — which by strict definition makes them *not* pure roguelikes.
- *Test for:* the design doc states exactly what is run-local vs. permanent; every reward the generator emits is **tagged one or the other** at creation; no ambiguous middle state.
- *Failure mode:* **identity confusion** — mixing persistence models so neither the mastery payoff (pure roguelike) nor the always-advancing payoff (roguelite) lands.

**Rule 3.2 — Make failure produce progress, but never trivialize the skill core.** Permanent upgrades should widen options and soften variance, not remove the need to play well.
- *Exemplar:* Hades **Mirror of Night** (respec-able permanent upgrades); Rogue Legacy manor/skill tree (rising costs; Offshore Bank Account retains gold). *Counter-caution:* purists hold that meta-progression can make a win feel unearned vs. a pure ascension.
- *Test for:* with max meta-progression but median skill, the hardest content is still *losable*; with min meta-progression but high skill, early content is still *winnable* — skill stays load-bearing.
- *Failure mode:* **grind-to-win** (power creep out-levels skill, collapsing stakes) and its inverse, **mandatory grind walls** gating skilled players.

**Rule 3.3 — Integrate death diegetically: canonize failure in the fiction.** Give death an in-world explanation so repetition is narrative, not a mechanical reset; advance story *through* deaths.
- *Exemplar:* Hades. Kasavin: make "players understand that failed runs are actually okay," built "into the fiction itself." Zagreus dies, returns to the House of Hades, characters remember, bosses evolve dialogue. Kasavin (VICE): "If the player doesn't forget their deaths and learns from them, so should the protagonist." This solved the roguelite narrative contradiction (story needs continuity; runs need resets).
- *Test for:* there is an in-world reason the protagonist returns; NPCs/world acknowledge prior deaths; some narrative content is gated behind and revealed by failed runs.
- *Failure mode:* **ludonarrative dissonance of death** — story says death is final, mechanics say respawn, with no reconciliation (the "why am I back?" break). (See `worldbuilding-and-lore` §5.)

**Rule 3.4 — Use meta-progression to pace retention and difficulty together.**
- *Exemplar:* Hades is "better before you're good at it" — slower players see more story, conversations, boons; Dead Cells BSC and Hades Heat re-extend the challenge for masters.
- *Test for:* the unlock curve front-loads identity-shaping upgrades and reserves later unlocks for optimization; a difficulty-escalation path exists past "first win."
- *Failure mode:* **content exhaustion** (meta-progression finishes before interest, leaving a flat game) or **front-loaded trivialization**.

> **Procedural / AI implication.** Diegetic death is the **hardest element to procedurally generate** — acknowledgement-of-prior-death is bespoke narrative. Recommended split: **hand-author the death/return frame and the persistent hub** (the House-of-Hades equivalent); let the engine generate the *content between* deaths. Procedurally **select/parameterize NPC death-acknowledgement lines from a hand-authored pool** keyed to run-state (death count, last killer, last region) — this approximates Hades' reactivity without free-form generation risk. Tag every generated reward run-local or permanent at creation so the economy stays coherent across deaths.

---

## Area 4 — The open question: single-save + long-form open world

> **⚠️ HEAVY FLAG #1.** The seed literature (Hades, Spelunky, Dead Cells, FTL, Slay the Spire, the Berlin Interpretation, Sid Meier, loss-aversion research) is built on **short-run** structures. It does **NOT** directly address single-save permadeath over a **long-form 3D open world**. Everything below is **extrapolation** from adjacent genres. Treat each rule as a **HYPOTHESIS to be playtested**, not established practice.

The core tension: long-form open worlds accrue dozens of hours of *unique, non-regenerating* progress. Pure permadeath there triggers maximum **loss aversion** (Kahneman & Tversky, 1979) — catastrophic, motivation-killing loss, not productive stakes. The genres that *have* solved long-form + permanent loss are **dynasty/succession and world-persistence games, not action-RPGs.** The move: **separate what dies (the character) from what persists (the world-state), and justify both diegetically.**

**Rule 4.1 — Make the *character* mortal but the *world-state* persistent.** On death, permanently lose the avatar (and avatar-bound progress) while preserving explored map, faction relationships, world events, economy, and finite-loot locations.
- *Exemplar:* Dwarf Fortress — the procedurally generated world is simulated once and persists across the death of dwarves and whole forts; an abandoned fort becomes a reclaimable **ruin**, a retired one a living NPC settlement, all recorded in **Legends mode**. ("Boatmurdered," the 14-player succession game, became foundational emergent-storytelling lore.)
- *Test for:* after avatar death, world-state diffs persist (map reveal, faction standings, consumed/looted legendaries, altered settlements); only avatar-bound state resets.
- *Failure mode:* **total-wipe despair** — treating long-form death like a short-run reset, destroying dozens of hours and motivation.

**Rule 4.2 — Use a legacy/succession frame so continuity is diegetic, not a menu.** On death, transfer control to a successor (heir, reincarnation, new cursed undead) who inherits *some* predecessor progress and the persistent world.
- *Exemplar:* **Crusader Kings II/III** — the strongest commercial proof the protagonist can *permanently die* while the game continues for centuries; you continue as your dynastic heir, land/titles/gold persist, personal traits/skills die with the individual, and dynasty extinction is the true game-over. **Rogue Legacy** is the action bridge: heirs inherit gold/upgrades/classes/blueprints, each with quirky genetic traits, framed as children answering "for their father's sins."
- *Test for:* a successor pipeline exists; the design states exactly what an heir inherits (world-state + a subset of avatar progress) vs. loses (avatar-specific power); lineage extinction is the true game-over.
- *Failure mode:* **heir irrelevance** (successors so weak/disconnected it feels like a restart) or **heir over-inheritance** (removes all stakes from death).

**Rule 4.3 — Provide a diegetic continuity justification grounded in world lore, introduced early.** The reason death is/isn't reversible must be an in-world fact woven in from the start.
- *Exemplar:* Dark Souls' Undead Curse (player and enemies share the rule that the cursed return at bonfires, losing held souls; quitting for good = going Hollow); Demon's Souls' **World Tendency** persists per-region state from the player's deaths/deeds. (Morrowind established the open-world persistence-of-consequence frame but used conventional saves.) For the running example, pick *one* lore engine — token economy, reincarnation, or curse — that explains both persistence and mortality.
- *Test for:* a single stated in-world mechanism explains continuity after death; it's introduced early; mechanics and fiction agree (no save UI contradicting the lore).
- *Failure mode:* **bolt-on lore** — a respawn justification invented after the mechanics ("why am I back?" at open-world scale). (→ `worldbuilding-and-lore` §5.)

**Rule 4.4 — Consider partial persistence / corpse-recovery to soften loss without erasing it.** Death drops a recoverable cache at the death site, retrievable on a risky run, while keeping core stakes.
- *Exemplar:* Dark Souls bloodstain (drop all held souls at the death spot; recover by returning without dying, lose permanently on a second death). NetHack **bones files** (a dead character's level + ghost + gear may load into a *future* player's game — one death becomes persistent world content; note artifacts are de-artifacted on reload to avoid duplication).
- *Test for:* death produces a recoverable artifact at a known location; recovery is non-trivial but possible; a second death/timeout makes the loss permanent.
- *Failure mode:* **frictionless recovery** (no stakes) vs. **impossible recovery** (corpse run harder than what killed you = pure salt). For finite loot, dropped *legendaries* must never be permanently destructible without an explicit recovery window, or the finite-loot guarantee breaks.

**Rule 4.5 — Reconcile permadeath with the finite-legendary-loot economy explicitly.** Define what happens to finite, non-regenerating legendaries when their holder dies — destroyed, dropped, world-returned, or inherited — and keep the world's *total* legendary inventory consistent.
- *Exemplar (extrapolated — no direct precedent):* closest is NetHack bones (dead character's items re-enter the world for others) and Dwarf Fortress (a slain legend's artifact persists in world history / at the death site). For the running example: a dead hero's legendary should **re-enter the persistent world** (on the corpse, with an heir, or in a faction's hands) rather than vanish.
- *Test for:* an inventory-conservation audit — sum of all legendaries across world + corpses + heirs + NPCs is **invariant** across deaths (none silently created/destroyed except by an explicit logged rule).
- *Failure mode:* **economy leak** — legendaries destroyed on death (world runs dry → softlocks completion) or duplicated via heir/respawn (inflation destroys their meaning). Either breaks the signature finite-loot pillar (→ `rpg-systems`).

**Rule 4.6 — Default to opt-in single-save for long-form; gate hardcore permadeath behind explicit consent.** Given the unproven nature of long-form single-save, ship permadeath as a *chosen* mode with a persistence/legacy default.
- *Exemplar:* Diablo II/IV Hardcore, XCOM Ironman (single auto-overwriting save), Caves of Qud's three modes — all ship lethal permadeath as an opt-in subculture, not the default, because mandatory permadeath restricts audience.
- *Test for:* if single-save permadeath is default, telemetry must show acceptable retention past the first catastrophic loss; otherwise ship a legacy/heir default and an opt-in "true permadeath" toggle.
- *Failure mode:* **audience cliff** — mandatory long-form permadeath drives mass churn at the first big loss, with no data to justify it.

> **⚠️ HEAVY FLAG #2 — Area 4 procedural / AI-authoring note.** This is **double-novel**: single-save long-form open world is unproven, AND AI-authoring it adds risk the literature never addresses. Compounded dangers:
> - **Generated unfairness is unrecoverable here.** One unfair death can cost the whole game, not minutes. Area 1's fairness backbone is **non-negotiable and must be stricter.**
> - **World-state persistence + procgen is a state-explosion problem.** The generator must persist and stay coherent with an ever-growing diff (deaths, ruins, consumed loot, faction shifts) — the Dwarf Fortress "Legends" challenge at action-RPG fidelity. **Hand-author the persistence schema; let AI fill content within it.**
> - **Finite-loot conservation must be machine-checked** every generation/death cycle (Rule 4.5) — an unconstrained generator will create or destroy legendaries.
> - **Diegetic continuity can't be fully generated.** Hand-author the curse/heir/reincarnation frame and the persistent hub; parameterize generated content to reference it.
> - **There is no published telemetry** for this configuration. **Instrument retention-after-catastrophic-loss and treat all of Area 4 as an experiment with kill-switches** (fall back to legacy/heir default if churn spikes).

---

## Area 5 — Caveats (carried from the source synthesis)

- **Area 4 is extrapolation, not established practice.** No shipped game closely matches single-save + long-form + high-lethality + finite-loot + AI-authored 3D open world. The succession/persistence analogs come from other genres; porting them is unvalidated. **Playtest everything in Area 4.**
- **Much community/secondary sourcing.** Some points (save-scumming taxonomy, permadeath-psychology, Demon's Souls World Tendency, Dark Souls bloodstain timing) are community/journalistic. Strongest primaries: Kasavin interviews (Inverse, Game Developer, VICE), Sid Meier GDC 2012, the Berlin Interpretation post, Fåhraeus on CKII, Jake Solomon on XCOM RNG (TIME 2012).
- **The Berlin Interpretation is contested and dated (2008);** it disclaims being prescriptive and designers reject it (Darren Grey, "Screw the Berlin Interpretation!"). Use it as vocabulary, not law.
- **Loss aversion is borrowed from behavioral economics** (Kahneman & Tversky, 1979); the mapping to long-form permadeath is plausible but not directly measured.
- **"Losing is Fun" is a community motto,** not an official Bay 12 slogan; Tarn Adams' verifiable related quote is "If you're bored, you win."
- **Specific numeric mechanics drift across patches.** Hades' Deus Ex Machina (20% base, +2%/death, 80% cap) and Dead Cells' 5-BSC/Malaise values reflect cited wiki state — re-verify against the current build before treating as fixed targets.

---

## Recommendations (staged, with thresholds)

**Stage 0 — Foundations (before generating any content).**
1. Build the **fairness/readability backbone** first: per-tier telegraph budgets, guaranteed escape/counter-options, lighting/LoS minimums, hazard-density ceilings, an automated per-encounter survivability check (Area 1).
2. Implement **seeded, per-subsystem deterministic RNG** (combat / loot / world-gen as separate reproducible streams) so the single save is scum-proof and debuggable.
3. Author the **persistence schema** (avatar-bound vs. world-state) and the **legendary-loot conservation ledger** (Rules 4.1, 4.5).

**Stage 1 — Prove the core loop short before going long.**
4. Prototype combat as **short, high-lethality, fully-readable** encounters with diegetic death and a hand-authored hub. Validate deaths feel fair (telemetry: deaths attributable to player error, not RNG) BEFORE scaling to open world.
5. Ship **opt-in difficulty** (global lethality scalar; a God-Mode-style 20%→80% stacking assist) and a **diegetic death/return frame** from day one (Rules 2.2, 3.3).

**Stage 2 — Introduce long-form persistence as the experiment.**
6. Implement the **legacy/succession default** (heir/reincarnation inheriting world-state + a subset of avatar progress); gate **true single-save permadeath behind explicit opt-in** (Rules 4.2, 4.6).
7. Add **corpse-recovery / partial persistence** wired to the finite-loot ledger so legendaries are conserved (Rules 4.4, 4.5).

**Stage 3 — Instrument, then decide.**
8. Playtest specifically measuring **retention after the first catastrophic long-form loss.**

**Thresholds that change the plan:**
- Playtest deaths attributable to **unfair/unreadable causes >~10%** → STOP scaling content; return to the Area 1 backbone.
- **Post-catastrophic-loss churn high** (big drop in next-session return vs. baseline) → demote single-save to opt-in; ship legacy/heir as default (Rule 4.6).
- A **dominant option (>~85% pick rate)** appears at a decision node → not "interesting"; regenerate or rebalance (Rule 1.2).
- The **legendary-conservation audit ever fails** → halt loot generation until the leak/duplication is fixed (Rule 4.5).
- Skilled players **complete meta-progression before content interest** → add opt-in escalation (a Heat/BSC analog) before adding grind (Rules 2.4, 3.4).

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Unfair death | Off-screen / unsignaled / un-haveable-info kills | Fairness backbone: signal + counter per hazard (1.1) |
| 1-frame uppercut | Unreadable instant lethal attacks | Telegraph budget scaled to lethality (1.3) |
| 95%-that-missed | Surfaced odds violated by hidden modifiers | Deterministic/transparent RNG (1.4) |
| Long-run-death cliff | Hours of unique progress lost to one death | Short loops; persist world-state (2.1, 4.1) |
| Binary easy mode | Assist deletes the core loop | Stacking, opt-in, judgment-free assist (2.2) |
| Scum-faucet | Save-anywhere + visible RNG | One enforced anti-scum model (2.3) |
| Pure-reset | Lose everything, learn nothing | Every death teaches or banks progress (2.5) |
| Identity confusion | Mixed persistence models | State run-local vs. permanent explicitly (3.1) |
| Grind-to-win | Power out-levels skill | Keep skill load-bearing at max meta (3.2) |
| Death dissonance | Story says final, mechanics respawn | Canonize death in the fiction (3.3) |
| Total-wipe despair | Long-form death = short-run reset | Character mortal, world persistent (4.1) |
| Heir irrelevance | Successor feels like a restart | Inherit world-state + subset of progress (4.2) |
| Bolt-on lore | Respawn justified after the fact | Diegetic continuity, introduced early (4.3) |
| Economy leak | Legendaries destroyed/duplicated on death | Conservation audit every cycle (4.5) |
| Audience cliff | Mass churn at first big loss | Opt-in permadeath; legacy default (4.6) |

---

*Distilled from a deep-research synthesis (Meier, Yu/Shafer, Kasavin, Solomon, the Berlin Interpretation, Kahneman & Tversky; CK/DF/Rogue Legacy/NetHack/Souls for succession). Areas 1–3 rest on shipped short-run games; **all of Area 4 is extrapolation requiring playtests** (HEAVY FLAGS preserved in §4). See `CONTRIBUTING.md` and `docs/research-prompts.md` §5.*
