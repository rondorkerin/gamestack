# Difficulty & Balancing — Guide

The discipline of eliminating dominant options and keeping players in the flow channel — from cost-curve math to DDA to accessibility assists. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Sid Meier's "Interesting Decisions" (GDC 2012), David Sirlin's *Playing to Win* (2001) and "Balancing Multiplayer Games" (Sirlin.net), Game Balance Concepts blog (Levels 3 & 9, 2010), Valve's L4D GDC 2009 talk (Michael Booth), Jesse Schell's *The Art of Game Design* 3rd ed. (2019), Csikszentmihalyi's *Flow* (1990), and Celeste accessibility reporting (gamedeveloper.com, VICE, Celeste Wiki). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source · ❌ refuted / insufficient evidence. Full citations in the research doc (`docs/research/round1-universal/U5`).

> **The central law:** *balance exists to preserve interesting decisions.* A dominant strategy — one strictly better than all alternatives — makes every decision trivial. The invariant "no single option dominates" is testable and, uniquely among design disciplines, automatable: build the payoff matrix, run the dominance check, gate the ship.

> For the foundational "interesting decision" theory underneath see `game-design-fundamentals`. For the combat-specific application (encounter difficulty, damage tier) see `combat-design`. For loot and economy power-budget see `rpg-systems`. For session-arc pacing see `pacing-and-the-player-journey`. For procgen gating see `procgen-review`. For fairness under lethality see `permadeath-and-lethality`.

---

# Sub-domain A — Dominant Strategies & Degenerate Cases

> **What balance exists to eliminate:** A dominant strategy produces a better expected outcome than all alternatives regardless of opponent actions. In game-theory terms, strategy A strictly dominates B if A's payoff exceeds B's in every game state. ✅ (Sirlin, *Playing to Win*, 2001; Sid Meier, GDC 2012: "a choice with an obvious correct answer is not a decision")

## A.1 Eliminate strictly dominant options before shipping

No single option should produce a strictly better outcome than all alternatives in all contexts. Sirlin: "A dominant move is strictly better than any other you could do, so its very existence reduces the strategy of the game." ✅

- **Exemplar:** *Civilization's* Infinite City Sprawl (ICS) — early Civ penalized city *size* but not city *count*, making many small cities the strictly optimal land strategy. Civ V+ added per-city amenity costs, diplomatic penalties, and diminishing returns. The mechanic was patched because no other strategy competed. ✅
- **Test for:** For each option, compute its expected value across a representative sample of opponent/game states. If one option's EV exceeds all others by ≥10% in ≥80% of tested states, flag as a dominant candidate. For card/unit/weapon systems: pick rate ≥75% of all slots filled by one option = immediate flag.
- **Failure mode:** *Degenerate meta* — the game reduces to "play X or lose to X"; expert play becomes uninteresting and novice play becomes confusing when X is discovered.

## A.2 Use Sirlin's viability criterion to audit option pools

"A multiplayer game is balanced if a reasonably large number of options available to the player are viable" — viable meaning competitive at expert play. ✅ The five-tier model (God / Strong / Fair / Weak / Garbage) gives a practical heuristic: populate only the middle three tiers; keep God and Garbage empty.

- **Exemplar:** *Street Fighter II* had an effectively God-tier Guile with infinite move chains until patch. Capcom's revisions — charge-timing changes, reduced sonic boom recovery — moved Guile into Strong without making him unplayable. ✅ (Sirlin, *Playing to Win*; Sirlin.net Part 2)
- **Test for:** List all selectable options. Categorize by tournament win rate or expert pick rate. If the top tier contains <20% of options but >70% of wins, the God-tier problem exists.
- **Failure mode:** *God-tier lock-in* — a tier-0 option the community demands be banned, shrinking the effective game.

## A.3 Dominant strategies in multiplayer are more destructive than in single-player

In single-player, dominance makes the game easy but completable. In multiplayer, it creates an arms race and eliminates all other viable playstyles. ✅

- **Exemplar:** *Hearthstone's* "Undertaker Hunter" (2014) — a curve of one-drops with dominant synergy turned competitive play into either "play this deck" or "lose." Blizzard nerfed Undertaker's stat boost. ⚠️ (widely documented in community postmortems; no formal Blizzard case study published)
- **Test for:** In multiplayer, run tournaments between AI agents with unrestricted option access. One strategy winning ≥60% of matches across all matchups = dominant candidate.
- **Failure mode:** *Solved metagame* — competitive play collapses to a single answer, repelling players who invested in other options.

**→ Procedural / headless implication.** A generator building card decks, weapon loadouts, or faction packages must run a **dominance check** on output before finalizing: compute EV for each generated option O across a representative sample of opponent strategies. If E[O] > E[any other option] in >80% of samples, reject and regenerate with a variance penalty on O's cost or benefit. This is an automated pre-ship gate, not a human review step.

---

# Sub-domain B — Balance Structures: Symmetric, Asymmetric, Intransitive, Cost-Curve

> **The three structural approaches:** symmetric (identical starting options, richness emerges from play), asymmetric (different options with equal expected value), intransitive (no option dominates; each beats at least one other). Most real games combine all three at different layers. ✅ (Sirlin Part 1 — spectrum of asymmetry; Game Balance Concepts blog Level 9)

## B.1 Symmetric balance is simpler to verify but less strategically rich

All players draw from the same pool; balance verification reduces to checking that starting conditions are truly equal.

- **Exemplar:** *Go* — identical pieces, all strategic richness from play, not starting asymmetry. *Chess* acknowledges the white-moves-first tempo advantage as a feature. ✅
- **Test for:** Are all starting options drawn from the same pool under the same constraints? If yes, verify true equality (no hidden tempo or positioning break).
- **Failure mode:** *False symmetry* — options appear symmetric but a hidden advantage breaks equilibrium (first-player advantage in some board games).

## B.2 Asymmetric balance requires expected-value equivalence across all starting options

Different starting options must produce equal win rates against a random opponent at the same skill level. The farther a game is on the asymmetry spectrum, the more rigorously it must care about starting-option fairness. ✅ (Sirlin Part 1)

- **Exemplar:** *StarCraft II* (Terran / Zerg / Protoss) — Blizzard issues balance patches driven by tournament win-rate data. Protoss historically underperformed at high level (round-of-16 representation roughly half that of Zerg at peak imbalance) before corrective patches adjusted unit cost, timing windows, and production mechanics. ✅ (aligulac.com balance report; illiteracyhasdownsides.com)
- **Test for:** Run N=1000 simulated matches between each pair of starting options with equal-skill agents. Win rates outside 45–55% warrant investigation; outside 40–60% warrant immediate patch.
- **Failure mode:** *Faction lock-out* — one starting option is objectively weaker and competitive players abandon it.

## B.3 Intransitive (rock-paper-scissors) balance creates strategic cycles where no option dominates

Structure options so every viable choice beats at least one other and loses to at least one other. Optimal play requires reading context, not computing an absolute best answer. ✅ (Game Balance Concepts blog, Level 9)

- **Exemplars:**
  - *Fighting games*: normal attacks beat blocks → blocks beat throws → throws beat normal attacks. (Street Fighter, Tekken series) ✅
  - *RTS unit triangles*: fliers beat infantry → infantry beats archers → archers beat fliers. (StarCraft, Age of Empires lineage) ✅
  - *CCG archetypes*: aggro beats control → control beats combo → combo beats aggro. (Mark Rosewater, "Making Magic," Wizards of the Coast) ✅
- **Test for:** Build a payoff matrix for all strategic options. Verify that no row is weakly dominant (all payoffs ≥ all others). Verify that a mixed-strategy Nash equilibrium exists with all options having positive probability mass. An option at 0 probability in equilibrium is strictly dominated — rework or remove it.
- **Failure mode:** *Broken cycle* — one node beats two others, converting the intransitive loop into a transitive one with a new dominant strategy.

## B.4 Cost-curve balance: assign every effect a resource-unit value and verify costs ≈ benefits

For transitive systems (weapons, units, skills), convert all costs and benefits to one resource unit. Verify `Σ(costs) + Σ(benefits) ≈ 0`. Above-curve items have undercounted costs; below-curve items waste design space. ✅ (Game Balance Concepts blog, Level 3)

- **Exemplar:** *Magic: The Gathering's* mana cost system. Rosewater's team identified that cards above 5 mana should have dramatically higher effect-per-mana because each additional mana after 5 costs multiple turns of opportunity. The mana-curve shift at 5+ CMC is now a documented design rule. ✅ (magic.wizards.com "Making Magic"; Game Balance Concepts blog Level 3)
- **DPS normalization formula (weapon example):** `Sustained DPS = (base_damage / (1/fire_rate + reload_time)) × (1 + crit_chance × (crit_multiplier - 1))`. All same-tier weapons should fall within a ±15% band. ✅
- **Four curve types** (after Game Balance Concepts Level 3): *Linear* (rare); *Increasing-cost* (RPG stat scaling); *Decreasing-cost* (opportunity-cost design); *Threshold/custom* (MTG's 5-mana cliff).
- **Test for:** Build a balance spreadsheet listing every option with resource cost and all numerical effects translated to the same unit. Options that produce outlier benefit-to-cost ratios (> mean + 2σ) are above-curve; flag for nerf or cost increase.
- **Failure mode:** *Power creep* — new options must be above-curve to feel rewarding, shifting the average upward until old content is obsolete. Rosewater's mitigation: push power up in one game area per release while pulling it down in others to keep the global average constant. ⚠️ (community analysis of Rosewater's public statements; no formal Wizards document located)

## B.5 Lenticular design encodes complexity that does not overwhelm beginners

A single option can be simple to a novice (face value) and reveal strategic depth to an expert (hidden conditional power). This widens the viable-options pool without raising the entry barrier. ✅ (Rosewater, "Lenticular Design," magic.wizards.com, March 2014)

- **Exemplar:** *MTG's Black Cat* — a 2-mana 1/1 with a minor death effect to a beginner; a recursion/discard/sacrifice engine target to an expert.
- **Test for:** Write two descriptions for each option: one for a first-time player, one for an expert. If the expert description adds strategic value the beginner description omits, the option is lenticular. If both descriptions are identical, the option lacks complexity headroom.
- **Failure mode:** *Complexity that only punishes beginners* — advanced interactions that harm novices without rewarding experts (anti-lenticular).

**→ Procedural / headless implication.** A generator creating cards, weapons, or units should output a **cost-curve score** for each item and compare against the current mean ± tolerance. Items outside 1σ are adjusted before entering pools. For intransitive systems (factions, archetypes), the generator must output a payoff matrix and verify the cycle holds before finalizing. Lenticular "conditional upsides" can be procedurally attached to simple base effects — but verify the conditional does not make the item above-curve when the condition is easy to trigger.

---

# Sub-domain C — Difficulty Curves & Dynamic Difficulty Adjustment

> **The flow channel as the target:** Csikszentmihalyi (*Flow*, 1990) defines three states: challenge >> skill → anxiety; challenge ≈ skill → flow; challenge << skill → boredom. The designer's job is to keep the player in the narrow flow band while both skill and challenge increase across the game's arc. ✅ (Csikszentmihalyi 1990; Schell, *The Art of Game Design*, 3rd ed., Lens #18)

## C.1 Model the difficulty curve as a rising wave, not a flat line

Challenge should trend upward but oscillate: brief relief troughs below the current skill level (the "power fantasy moment") followed by rising challenge. Flat lines stay in-channel but produce less engagement depth.

- **Exemplar:** *Super Mario Bros.* (1985) — World 1-1's single Goomba on flat ground teaches the jump. 1-2 introduces hazards. 1-3 oscillates: enemy-dense sections followed by clear platforms. The macro arc rises while micro troughs let players apply learned skills confidently. ✅ (Schell, Lens of Flow; gamedeveloper.com Flow Channel article)
- **Test for:** Plot a challenge estimate (enemy HP × count × speed, or equivalent) for every encounter in sequence. The trend line should rise. Variance around the trend should create recognizable peaks and troughs — not a monotonic climb (fatigue risk) and not purely random (anxiety spikes).
- **Failure mode:** *Difficulty spike* — one encounter with challenge >> current skill level, often a new mechanic introduced at full intensity without prior teaching.

## C.2 DDA must define its target metric, adjustment range, and update cadence before implementation

Dynamic Difficulty Adjustment adjusts game parameters in real time based on measured player performance. Before building any DDA system, answer: what does it measure, what does it change, how fast does it change, and is that visible to the player? ✅ (Wikipedia, Dynamic game difficulty balancing; gamedeveloper.com "Game Changers: Dynamic Difficulty")

- **Named DDA techniques** (after Wikipedia DDA article): *Parameter manipulation* (Hunicke & Chapman): adjust weapon damage, enemy HP, item availability directly. *Dynamic scripting* (Spronck et al.): probability weights on NPC behavior rules, updated per-encounter. *Reinforcement learning* (Andrade et al.): offline-trained agent adapts online to specific player. *Rubber-band physics* (Mario Kart): item quality and CPU speed tied to player rank.

## C.3 The L4D AI Director's "emotional pacing" model is the benchmark for cooperative DDA

The Director monitors each player's health, stress, encounter history, and skill signals in real time. It controls zombie spawn volume, spawn positions, item placement, enemy type mix, and music — targeting a *peak / relief / peak* emotional arc, not a fixed difficulty level. ✅ (Valve GDC 2009, Michael Booth; Wikipedia Left 4 Dead; gamedeveloper.com)

- **The Director's state machine:** build tension → sustained intensity peak → mandatory lull (clear area, items available) → build tension again. Lull duration is proportional to how severe the previous peak was.
- **Exemplar:** *Left 4 Dead* (2008) — a poorly-performing team (low health, many deaths) receives fewer special infected, more medkits, and shorter hordes. A well-performing team receives more Hunters and Tanks, fewer ammo drops, and longer hordes. Same map, wildly different encounter experience. ✅
- **Test for:** Record player health and death frequency per chapter against encounter intensity (zombie count per minute). If high-death runs show high intensity without lull insertion, the Director is failing to relieve pressure. If low-death runs never reach a peak, the Director is failing to challenge. Target: every chapter should contain ≥1 identifiable peak and ≥1 identifiable lull in the data.
- **Failure mode:** *Director blindness* — adjusting global parameters without tracking individual player stress, so one struggling player pulls the whole team into easy mode while others are underchallenged.

## C.4 RE4's hidden difficulty score adjusts on a 1–10 scale based on combat performance

*Resident Evil 4* (2005) maintains an internal difficulty score (default rank 6). Successful combat raises the score; repeated deaths lower it. Higher ranks increase enemy count, aggression, HP, and damage; lower ranks reduce them. ✅ (Wikipedia DDA article; CBR article on RE4 mechanics)

- The "fear AI" framing is a community misnomer — no Capcom document uses the term. ❌ The underlying system is well-documented; the "fear AI" label is not.
- **Failure mode:** *Discovered DDA exploitation* — players who learn the system deliberately die early to lock a low rank, then play through on trivially easy settings (documented in RE4 community).

## C.5 Visible difficulty meters maintain perceived fairness at the cost of player gaming the system

*God Hand* (2006) displays a skull meter that rises as the player successfully dodges and defeats enemies (four levels, top labeled "DIE!"). Players always know their current difficulty tier. ✅ (gamedeveloper.com "Game Changers: Dynamic Difficulty"; Wikipedia DDA article)

- Ernest Adams' rule: hide the DDA mechanism and players who discover it feel deceived; show it and players who are losing may feel mocked. Neither is wrong — it is a tone and audience decision.
- **Test for:** If DDA is hidden, write a player-facing rationale and verify the system cannot be exploited by deliberate underperformance. If DDA is visible, verify the top tier is still completable by skilled players and the bottom tier still teaches core mechanics.
- **Failure mode:** *Rubber-band reversion* — DDA so aggressively tracks performance that skilled play is immediately negated, breaking the skill-outcome correlation.

## C.6 Perceived fairness is as important as actual difficulty calibration

A mechanically fair game that feels arbitrary (surprise deaths, invisible hazards, hidden DDA that's discoverable and exploitable) fails the fairness test. A slightly unfair game that clearly communicates its rules and stakes can feel fair. ✅ (Rollings & Adams, cited in Wikipedia DDA; Civilization example from gamedeveloper.com "Balancing Act")

- **Exemplar:** *Civilization* openly discloses that higher difficulty gives the AI production bonuses. Players accept this as fair because *they were told*.
- **Test for:** Can a player accurately predict why they died / lost / were put at a disadvantage? If players attribute losses to "the game cheating," transparency is insufficient.
- **Failure mode:** *Opaque disadvantage* — hidden stat bonuses, unannounced difficulty scaling, or invisible rubber-banding the player cannot attribute to their own play.

**→ Procedural / headless implication.** A procedural encounter generator cannot manually tune each encounter. It must attach a **challenge estimate** to every generated encounter and validate the *sequence*, not just each piece in isolation. Required gates: (a) no encounter's challenge value may exceed 2.5× the current session-average without a preceding easier encounter that introduces the mechanic; (b) every N encounters must include at least one encounter with challenge < 0.7× session-average (the lull). Under single-save high-lethality, an unannounced procedural spike is a game-ending event — this constraint is non-negotiable. See `permadeath-and-lethality`.

---

# Sub-domain D — Difficulty Settings & Accessibility

> **Two distinct problems:** Difficulty settings match player preference to challenge level. Accessibility removes barriers that prevent play entirely (motor, cognitive, perceptual). These overlap but are not the same — good design addresses both independently. ✅ (gamedeveloper.com Celeste assists coverage; junkee.com Sekiro accessibility article)

## D.1 Granular per-axis assists outperform a small number of discrete difficulty modes

Discrete modes (Easy / Normal / Hard) bundle multiple difficulty axes (enemy damage, player HP, puzzle complexity, time pressure) that players may need at different levels. Granular assists let players tune each axis independently.

- **Exemplar:** *Celeste* (2018, Maddy Thorson) Assist Mode: game speed 50–100% in 10% steps; invincibility toggle; infinite stamina toggle; chapter skip. ✅ Each assist targets one skill axis — a player who struggles with reaction time but enjoys platforming can slow time without becoming unkillable.
  - "Assist Mode" was originally named "Cheat Mode"; the rename removed shame framing. ⚠️ (stated in interviews; second direct-quote source not located)
- **Test for:** List the distinct skill axes your game challenges (reaction time, resource management, spatial reasoning, memorization, build complexity). For each axis, is there a corresponding assist that reduces difficulty on that axis *without* nullifying the others? Any major axis without a corresponding assist is an accessibility gap.
- **Failure mode:** *Axis bundling* — a single "Easy Mode" simultaneously reduces reaction windows, enemy damage, and puzzle complexity, making the game trivially easy for a player who only needed help on one axis.

## D.2 Difficulty as design intent (the Miyazaki position) is valid — with explicit exclusion costs

Hidetaka Miyazaki on *Sekiro* (2019): "We don't want to include a difficulty selection because we want to bring everyone to the same level of discussion and the same level of enjoyment." ✅ (Steam community post; multiple news coverage)

- This is a coherent design position. It carries a documented accessibility cost: players with motor disabilities, RSI, or situational limitations cannot access the game at all.
- **The distinction:** difficulty (relative to player ability) ≠ accessibility (removal of barriers to play). A game can have high difficulty without accessibility barriers; removing barriers does not require reducing difficulty for players who want the full challenge.
- **Test for:** If no difficulty assists exist, is the design intent publicly stated and the fixed challenge demonstrably achievable through in-game mechanics by the target audience? If the game is "hard" due to unfixed jank rather than intentional design, no artistic position justifies the exclusion.
- **Failure mode:** *Difficulty as gate-keeping* — using "design intent" to avoid the engineering work of accessibility.

## D.3 Optional assists in single-player do not affect other players' experience

In single-player, a player using Celeste's Invincibility Toggle has no effect on any other player's experience. The "it diminishes my accomplishment if others can skip it" argument fails: achievements are personal. The counterargument applies only to multiplayer, where assists create actual unfairness.

- **Test for:** Do the proposed assists affect other players' experience (multiplayer)? If no → implement without guilt. If yes → scope assists to single-player modes or opt-in lobbies.
- **Failure mode:** *Bleeding accessibility into competitive modes* — applying single-player assists to ranked play where they create genuine unfairness.

## D.4 Naming and framing of difficulty options affect player self-perception

"Assist Mode" (Celeste) → neutral/supportive. "Story Mode" → implied you are not a gamer. "Easy Mode" → implied inferiority. The label affects whether players use the option. ✅ (Celeste accessibility articles; VICE interview)

- **Test for:** Does the difficulty label imply judgment of the player's skill or worth? If yes, replace with a neutral descriptor that describes what the mode does ("Exploration Mode," "Reduced Enemy Damage," "One-Hit Shield") rather than what it says about the player.
- **Failure mode:** *Shame friction* — players who need an assist don't use it because the label makes them feel inferior; they abandon the game instead.

**→ Procedural / headless implication.** For a procedurally generated game, difficulty settings must be implemented as multipliers on generated parameters (encounter budget, respawn timer, resource drop rate, DDA threshold) rather than separate content generation paths. A separate "easy" content path doubles the generation requirement and diverges the two experiences. Generate one base experience; apply per-axis scalars. This is also the only way to support granular per-axis assists at scale.

---

# Sub-domain E — Metrics-Driven Balance: Spreadsheets, Simulation, Telemetry

> **The three instruments:** (1) Pre-ship *spreadsheet / cost-curve analysis* — mathematical verification before the game runs. (2) Pre-ship *self-play simulation* — automated agents discover dominant strategies faster than human playtests. (3) Post-ship *telemetry* — real player data drives live tuning. All three are necessary; none alone is sufficient.

## E.1 Build and maintain a balance spreadsheet for every transitive system

For each option, record: resource cost, all quantifiable benefits (DPS, effective HP, resource generated, movement speed), and all quantifiable costs (cooldown, reload time, resource consumed). Normalize to a per-second or per-resource-unit value. Sort by cost. Outliers beyond ±2σ warrant investigation.

- **Exemplar:** *Diablo 3: Reaper of Souls* — the balance team used internal DPS spreadsheets to ensure set-item bonus powers scaled differently from base item stats, creating build-enabling outliers (intentionally above-curve) without making them mandatory for basic progression. ⚠️ (widely documented in community; Blizzard has not published the internal methodology)
- **Test for:** Does the balance spreadsheet exist and cover 100% of options in each transitive system? Do any options fall outside ±2σ of the DPS/value curve for their tier? Flag all outliers before each content ship.
- **Failure mode:** *Spreadsheet gap* — options added in expansion content without being added to the spreadsheet, creating unchecked above-curve items that dominate until discovered post-ship.

## E.2 Self-play simulation discovers dominant strategies faster than human playtest — but requires careful objective design

RL agents trained via self-play can enumerate dominant strategies across a combinatorial option space that human playtesting cannot cover in the same calendar time. The critical constraint: reward the agent for winning *via diverse strategies*, not just winning. ⚠️ (IEEE 2021 "Toward Automated Game Balance"; arxiv:2503.18748 "Simulation-Driven Balancing"; RuleSmith arxiv:2602.06232 — active research papers, not validated industrial standard as of 2026)

- **Exemplar (research-stage):** RuleSmith (arxiv:2602.06232, 2025) uses multi-agent LLMs to iterate on game rules and evaluate each iteration via simulated play, flagging dominant strategies in a test RTS within hours vs. weeks for human playtesting. ⚠️
- **Test for:** If using self-play balance testing, verify the agent explores all major strategy archetypes, not just the globally optimal one. Use population-based training (diverse agent pool) rather than single-agent self-play.
- **Failure mode:** *Strategy collapse in self-play* — two agents converge on the same dominant counter-strategy and stop exploring, producing a "balanced" result that is actually a two-option degenerate equilibrium.

## E.3 Post-ship telemetry targets are specific and numerical

Collect: option pick rate, win rate (per option, per mode), death location heatmaps, boss/level abandonment rate, session length by difficulty setting, time-to-first-successful-completion per challenge. ✅ (hitem3d.ai; daydreamsoft.com; videogamedevelopmentauthority.com)

- **Action thresholds (industry practice, ⚠️ practitioner heuristics, not peer-reviewed cutoffs):**
  - Pick rate ≥75% for one option in a slot → probable dominant strategy; investigate immediately.
  - Boss abandonment rate ≥60% → tuning emergency; reduce difficulty or add telegraph.
  - Win rate for one faction/character outside 45–60% at high elo → asymmetric balance problem.
  - Session length dropping ≥20% after a specific encounter → probable difficulty spike.
- **Exemplar:** *StarCraft II* — Blizzard's balance team uses tournament win-rate data (aligulac.com + Blizzard ladder statistics). A matchup win rate outside 48–52% for the top 200 players triggers a balance review. ✅
- **Test for:** Is logging in place for all listed metrics, computed per-session, per-patch, and per-skill-bracket? Are action thresholds defined in writing before shipping so the team acts on data rather than opinion?
- **Failure mode:** *Vanity metrics* — tracking total playtime or DAU without per-option or per-encounter breakdowns, yielding no actionable balance signal.

## E.4 Single-player and multiplayer balance require different instruments and different objectives

Single-player balance target: every player completes the critical path within the flow channel. The "opponent" is a fixed challenge — balance means calibrating that challenge to a range of skill levels.

Multiplayer balance target: all starting options produce equal expected win rates at matched skill levels. The "opponent" is another human — balance means ensuring no option creates an inherent advantage independent of skill. ✅ (Sirlin Part 1 definitions; hitem3d.ai comparison)

- **Failure mode (single-player):** *Tuning to the median* — right for the 50th-percentile player, brick wall for the bottom 25%, trivial for the top 25%. Fix with DDA or per-axis assists.
- **Failure mode (multiplayer):** *Skill-bracket blindness* — an option is balanced at pro level but dominant at casual level (or vice versa). Blizzard maintains separate balance targets for ladder brackets and pro play.

## E.5 Covert DDA used to drive monetization is an ethical and legal risk

EA's 2020 lawsuit alleged DDA in its sports games was covertly tuned to create frustrating experiences driving loot-box purchases. The lawsuit was voluntarily dismissed in 2021; EA denied all claims. ✅ (Wikipedia DDA article documents the lawsuit and dismissal)

- The design lesson: DDA tuned against player interests rather than for player experience will be discovered, and the reputational damage is severe.
- **Test for:** For every DDA parameter adjustment, can you write a player-facing justification phrased as "this makes your experience better because..."? If the honest answer is "this makes you frustrated so you spend money," the system is misaligned.
- **Failure mode:** *Monetization-aligned DDA* — adjustments that reduce player satisfaction to create spending pressure. Distinct from and more serious than rubber-banding (which merely reduces skill-outcome correlation).

**→ Procedural / headless implication.** A procedural game generating content at runtime must **instrument every generated encounter at generation time**: attach a predicted challenge score (enemy budget, estimated completion time, expected player health remaining); log it alongside actual outcome data; feed the delta back into generator parameters. This creates a closed-loop auto-tuning system: generated-challenge vs. measured-outcome discrepancy drives parameter updates. The update cadence must be bounded (no more than ±X% per session to prevent oscillation) and the range capped to prevent the system from disabling itself under extreme player performance.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Degenerate meta | One option with ≥75% pick rate; expert play solved | Dominance check; cost adjustment or nerf (A.1) |
| God-tier lock-in | Community demands a ban; other options abandoned | Five-tier audit; populate only Strong/Fair/Weak (A.2) |
| Solved multiplayer metagame | One strategy wins ≥60% of matches | AI tournament on all options; nerf or cost penalty (A.3) |
| Broken intransitive cycle | One faction/archetype beats two others | Payoff matrix; verify all options have positive Nash mass (B.3) |
| Power creep | Old content obsolete; every expansion above-curve | Cost-curve spreadsheet; Rosewater stairwell discipline (B.4) |
| Difficulty spike | One encounter causes abandonment or save-quit | Challenge estimate sequence gating; teaching encounter first (C.1) |
| Director blindness | One bad player trivializes for the whole coop team | Per-player tracking; per-player lull insertion (C.3) |
| Discovered DDA exploitation | Players die deliberately to lock easy rank | DDA visibility decision; exploit-resistance audit (C.4) |
| Rubber-band reversion | Skilled play immediately negated by the system | Cap adjustment rate and range; bound update cadence (C.5) |
| Opaque disadvantage | Players attribute losses to "the game cheating" | Communicate DDA rationale; disclose AI bonuses (C.6) |
| Axis bundling | "Easy Mode" makes game trivial for players needing one axis | Per-axis assist sliders; independent scaling (D.1) |
| Shame friction | Players don't use needed assists due to "Easy Mode" label | Neutral descriptive naming ("Exploration Mode") (D.4) |
| Spreadsheet gap | Expansion options above-curve; discovered post-ship | Spreadsheet covers 100% of options; gate before ship (E.1) |
| Self-play strategy collapse | Two agents reach degenerate equilibrium | Population-based training; diverse agent pool (E.2) |
| Vanity metrics | Playtime/DAU without per-option breakdown | Per-option pick rate, win rate, abandonment logging (E.3) |
| Monetization-aligned DDA | DDA creates frustration to drive spending | Player-facing justification test for every adjustment (E.5) |

---

## Caveats

- **Self-play RL for balance is research-stage, not industrial standard (as of 2026).** The cited papers (IEEE 2021, arxiv:2503.18748, RuleSmith 2025) describe research prototypes. No publicly documented case of a shipped AAA game using RL self-play as its primary balance instrument was found. Treat as a forward-looking technique requiring tooling investment.
- **Telemetry thresholds (75% pick rate, 60% abandonment) are practitioner heuristics.** They appear in practitioner guides, not peer-reviewed studies. Use as starting calibration points; tune to your genre and audience.
- **RE4's "fear AI" label is community-created.** Primary Capcom documentation does not use this term. The 1–10 difficulty score system is well-documented and verified ✅; the branding is not ❌.
- **The EA DDA / loot-box lawsuit (2020) was dismissed voluntarily; EA denied all claims.** Cited here as a cautionary design lesson, not a finding of wrongdoing.
- **Rosewater's "Escher Stairwell" and "Power Points Budget" are community-documented, not formal Wizards publications.** Consistent with Rosewater's public statements; no formal design document was located. ⚠️
- **Accessibility vs. difficulty is an evolving normative debate.** Celeste's granular-assists position and Miyazaki's fixed-challenge position are both documented here; neither is endorsed as the only correct answer. Surface the trade-off to the human designer rather than choosing by default.
- **Procedural content multiplies the surface area for accidental dominant strategies.** A generator can emit thousands of options per second, any of which could be inadvertently above-curve. The automated invariant approach described throughout this document is the field's best current answer, but it is under-studied in production settings as of 2026.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
