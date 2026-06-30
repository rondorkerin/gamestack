# U5 · Difficulty, Balance, and Tuning as a Measurable Craft

> **Consumer note:** This document is a knowledge base for an autonomous AI game-design agent (Claude Code, headless). Every rule is stated as a testable imperative. Failure modes are named. Each major claim is tagged: ✅ verified (≥2 independent/primary sources) · ⚠️ sourced-but-unverified (1 source or secondary) · ❌ refuted / insufficient evidence.

---

## TL;DR

- **Balance exists to preserve interesting decisions.** A dominant strategy — one strictly better than all alternatives — makes every decision trivial. "No single option dominates" is the central testable invariant; every balance tool is a method for enforcing it. (Sid Meier GDC 2012; David Sirlin, *Balancing Multiplayer Games*, 2009) ✅
- **Cost-curve math converts intuition to arithmetic.** For any transitive system, assign every effect (damage, range, speed, restriction) a resource-equivalent value and verify that cost ≈ benefit across all options. Items above the curve break balance; items below it are wasted design space. (Game Balance Concepts blog, Level 3) ✅
- **Intransitive (rock-paper-scissors) balance suppresses dominant strategies structurally.** When no single option beats all others, the optimal response changes with context, forcing real decisions. It is the primary balance tool for asymmetric multiplayer and deep combat systems. (Game Balance Concepts blog, Level 9; Sirlin, 2009) ✅
- **DDA targets the flow channel, not a fixed difficulty number.** Good dynamic difficulty adjustment maintains challenge ≈ skill (Csikszentmihalyi / Schell), but hidden adjustments risk the rubber-band effect and player distrust if discovered. Visibility is a design choice, not a technical afterthought. ✅
- **For a procedural / AI-authored game, every balance rule must be an automated test.** A headless generator cannot feel whether something is overpowered; it must measure it. Self-play simulation, dominance checks, and telemetry are the only reliable substitutes for human playtest at scale. ⚠️ (active research area; see §5)

---

## Key Findings

1. **A dominant strategy is a structural collapse.** Sirlin (2001): "A dominant move is strictly better than any other you could do, so its very existence reduces the strategy of the game." ✅ When one option dominates, all other options become noise; the game degenerates into either "always play X" or "try to beat X." The corollary from Sid Meier: a choice with an obvious correct answer is not a decision.
2. **Sirlin's viability criterion is the clearest operational definition of multiplayer balance.** "A multiplayer game is balanced if a reasonably large number of options available to the player are viable" — viable meaning "competitive at expert play." ✅ The five-tier model (God / Strong / Fair / Weak / Garbage) gives designers a practical heuristic: populate only the middle three tiers.
3. **Transitive mechanics require cost-curve verification; intransitive mechanics require payoff-matrix verification.** These are different mathematical checks, and conflating them produces imbalanced hybrids. A damage system is transitive and needs cost ≈ benefit arithmetic. A faction system is intransitive and needs a cycle where every option loses to at least one other. ✅
4. **The flow channel is narrow and shifts as players improve.** Csikszentmihalyi's three-channel model (boredom / flow / anxiety) requires continuous re-calibration: as player skill rises, challenge must rise in parallel or flow collapses into boredom. The "wave" approach (rising difficulty with periodic sub-peaks and troughs) outperforms a flat gradient in long-form games. ✅ (Schell, *The Art of Game Design*, 3rd ed.)
5. **The L4D AI Director is the canonical DDA implementation.** It tracks player health, stress, and encounter history in real time and adjusts zombie spawn volume, placement, and item availability to maintain a peak/lull/peak emotional arc — "procedural narrative." ✅ (Valve GDC 2009, confirmed by Wikipedia, gamedeveloper.com)
6. **RE4's difficulty system is hidden and performance-graded, not "fear AI."** The game maintains a 1–10 internal score based on combat performance (hits landed, deaths, aggression dodged); score shifts adjust enemy count, damage, behavior, and item drop rates. The "fear AI" framing is a community misnomer — no Capcom document uses the term. ✅/❌ (RE4 system confirmed ✅ from Wikipedia DDA article + CBR; "fear AI" label ❌ — not in any primary source)
7. **Granular assists outperform discrete difficulty modes for accessibility.** Celeste's Assist Mode (game speed 50–100% in 10% increments, invincibility, infinite stamina, chapter skip) allows players to tune individual axes of challenge independently, removing the binary "play on Normal or give up." ✅ (Gamedeveloper.com; Celeste Wiki; VICE)
8. **Telemetry closes the single-player balance loop post-ship.** Pick rates, win rates, death locations, and abandonment rates are the primary signals for live balance tuning. A character/weapon/boss with ≥60% abandonment rate is a tuning emergency; ≥75% pick rate for a single option flags probable dominance. ⚠️ (thresholds from industry practice cited in hitem3d.ai / daydreamsoft.com; not from a peer-reviewed study)
9. **Self-play RL can automate balance discovery pre-ship, but requires careful objective design.** Reinforcement-learning agents trained via self-play uncover dominant strategies faster than human playtests (IEEE 2021 paper; arxiv:2503.18748). The objective must penalize dominated options surviving — not just reward winning — or the agent finds exploits rather than balance. ⚠️ (research-stage; commercial deployment rare as of 2026)
10. **The rubber-band effect is justified only when transparently communicated.** Mario Kart's item distribution is publicly known; Civilization AI difficulty bonuses are disclosed in-UI; both survive player trust. Hidden DDA that dramatically reverses player skill ordering is perceived as "the game cheating" and erodes satisfaction (Rollings & Adams, cited in Wikipedia DDA article). ✅

---

## Details

### Sub-domain 1: Dominant Strategies and Degenerate Cases

> **What balance exists to eliminate:** A dominant strategy is an option that produces a better expected outcome than all alternatives, regardless of opponent actions. In game-theory terms: strategy A strictly dominates B if A's payoff exceeds B's payoff in every possible game state. ✅ (Strategic dominance, Wikipedia; Game Balance Concepts blog)

---

**Rule 1.1 — Eliminate strictly dominant options before shipping.**

No single option should produce a strictly better outcome than all alternatives in all contexts. If players converge on a single strategy at expert play, that strategy is probably dominant.

- **Exemplar:** *Civilization's Infinite City Sprawl (ICS)*. Early Civilization penalized city *size* (unhappiness) but not city *count*, making it strictly optimal to found many small cities. Later titles (Civ V+) added per-city amenity costs, diplomatic penalties for rapid expansion, and diminishing returns on city count. The mechanic was patched because no other land strategy competed. ✅ (Documented in GUIDE.md of this pack; widely cited in Civ design retrospectives)
- **Test for:** Run a strategy enumeration across all options in the decision space. If one option's expected value exceeds all others by ≥10% in ≥80% of tested game states, flag it as a dominant candidate. For card/unit/weapon systems: if pick rate in playtests exceeds 75% of all slots filled by that single option, flag immediately.
- **Failure mode:** *Degenerate meta* — the game reduces to "play X or lose to X." Expert play becomes uninteresting; novice play becomes confusing when X is discovered.

**Rule 1.2 — Use Sirlin's five-tier model to audit option viability.**

Maintain three populated tiers (Strong / Fair / Weak) and keep the top (God: must-play) and bottom (Garbage: never-play) tiers empty.

- **Exemplar:** *Street Fighter II* had an effectively God-tier Guile with infinite move chains until patch. Capcom's revisions — charge-timing changes, reducing sonic boom recovery — moved Guile into Strong without making him unplayable. ✅ (Sirlin, *Playing to Win*, 2001; Sirlin.net Balancing Multiplayer Games Part 2)
- **Test for:** List all selectable options (characters, weapons, builds, decks). Categorize each by tournament win rate or expert-play pick rate. If the top tier contains <20% of options with >70% of wins, the God-tier problem exists.
- **Failure mode:** *God-tier lock-in* — a tier-0 option that the community demands be banned, shrinking the effective game.

**Rule 1.3 — Dominant strategies that exist in multiplayer are more destructive than in single-player.**

In single-player, a dominant strategy makes the game easy but completable. In multiplayer, it creates an arms race and eliminates all other viable playstyles.

- **Exemplar:** *Hearthstone*'s "Undertaker Hunter" deck (2014) — a curve of one-drops with a dominant synergy turned competitive play into either "play this deck" or "lose." Blizzard nerfed Undertaker's stat boost from triggering deathrattle minions. ⚠️ (widely documented in community postmortems; Blizzard has not published a formal case study)
- **Test for:** In multiplayer contexts, run tournaments between AI agents with unrestricted option access. If one strategy wins ≥60% of matches across all matchups, it is a dominant candidate.
- **Failure mode:** *Solved metagame* — competitive play collapses to a single answer, repelling players who invested in other options.

> **Procedural implication:** A generator building card decks, weapon loadouts, or faction packages must run a dominance check on its output before finalizing. The check: for each generated option O, compute its expected value across a representative sample of opponent strategies. If E[O] > E[any other option] in >80% of samples, reject and regenerate with a variance penalty applied to O's cost or benefit. This check must run as an automated pre-ship gate, not a human review.

---

### Sub-domain 2: Balance Structures — Symmetric, Asymmetric, Intransitive, Cost-Curve

> **The three structural approaches:** symmetric (identical starting options), asymmetric (different starting options with equal expected value), and intransitive (no option dominates; each beats at least one other). Most real games combine all three at different layers.

---

**Rule 2.1 — Symmetric balance is simpler to verify but less strategically rich.**

When all players start with identical options, balance verification reduces to checking that starting conditions are truly equal. The cost is reduced strategic variety.

- **Exemplar:** *Go* (19×19 grid, identical pieces) is symmetric at the starting condition level; all strategic richness emerges from *play*, not from starting asymmetry. *Chess* is similarly symmetric in piece sets (with the white-moves-first tempo advantage acknowledged as a feature, not a bug). ✅ (Game balance — Grokipedia; Sirlin Part 1 — spectrum of asymmetry)
- **Test for:** Are all starting options (characters, factions, decks, classes) drawn from the same pool with the same constraints? If yes, balance is structural. If no, asymmetric rules apply.
- **Failure mode:** *False symmetry* — options appear symmetric but a hidden tempo or positioning advantage breaks equilibrium (e.g., first-player advantage in some board games).

**Rule 2.2 — Asymmetric balance requires expected-value equivalence across all starting options.**

Different starting options must produce equal win rates against a random opponent at the same skill level. Sirlin: "The farther a game is to the right of the asymmetry spectrum, the more it needs to care about balancing the fairness of the different starting options." ✅

- **Exemplar:** *StarCraft II* (Terran / Zerg / Protoss) — Blizzard issues balance patches driven by tournament win-rate data. Protoss historically underperformed in high-level play (round-of-16 representation roughly half that of Zerg at peak imbalance periods) before corrective patches adjusted unit cost, timing windows, and production mechanics. ✅ (aligulac.com balance report; illiteracyhasdownsides.com analysis)
- **Test for:** Run N=1000 simulated matches between each pair of starting options with equal-skill agents. Win rates outside 45–55% warrant investigation; outside 40–60% warrant immediate patch.
- **Failure mode:** *Faction lock-out* — one starting option is objectively weaker, so competitive players converge on the strongest options and the others are abandoned.

**Rule 2.3 — Intransitive (rock-paper-scissors) balance creates strategic cycles where no option dominates.**

Structure options so every viable choice beats at least one other and loses to at least one other. The result: optimal play requires reading the opponent, not computing an absolute best answer. ✅ (Game Balance Concepts blog, Level 9)

- **Exemplars:**
  - *Fighting games*: Normal attacks beat blocks → Blocks beat throws → Throws beat normal attacks. (Street Fighter, Tekken series) ✅
  - *RTS games*: Fliers beat infantry → Infantry beats archers → Archers beat fliers. (StarCraft, Age of Empires lineage) ✅
  - *CCG deck archetypes*: Aggro beats Control → Control beats Combo → Combo beats Aggro. (*Magic: The Gathering* meta design; Mark Rosewater, "Making Magic" column, Wizards of the Coast) ✅
- **Test for:** Build a payoff matrix for all strategic options. Verify that no row is weakly dominant (all payoffs ≥ all others). Verify that a mixed-strategy Nash equilibrium exists with all options having positive probability mass. If any option drops to 0 probability in equilibrium, it is strictly dominated and should be reworked or removed.
- **Failure mode:** *Broken cycle* — one node in the intransitive loop beats two others, converting an intransitive structure into a transitive one with a new dominant strategy.

**Rule 2.4 — Cost-curve balance: assign every effect a resource-unit value and verify costs ≈ benefits.**

For transitive systems (weapons, units, skills), convert all costs and benefits to a single resource unit (damage-per-mana, DPS-per-cost, stat-per-gold). Verify that the sum of costs plus the sum of benefits ≈ 0. ✅ (Game Balance Concepts blog, Level 3 — direct source fetch verified)

- **The core equation:** `Σ(costs) + Σ(benefits) = 0` where costs are negative and benefits positive, all in the same resource unit. Above-curve items are undercounted costs or overcounted benefits; below-curve items are the reverse.
- **Exemplar:** *Magic: The Gathering*'s mana cost system. Early Magic used a flat cost-power curve; over years Rosewater's team identified that cards above 5 mana should have dramatically higher effect-per-mana than low-cost cards, because each additional mana after 5 costs multiple turns of opportunity. The mana curve shift at 5+ CMC is now a documented design rule. ✅ (magic.wizards.com "Making Magic" column; Game Balance Concepts blog Level 3)
- **Four curve types** (after Game Balance Concepts, Level 3):
  - *Linear*: every resource unit buys the same benefit (rare in real games).
  - *Increasing-cost*: progressive premium for additional benefit (common in RPG stat scaling).
  - *Decreasing-cost*: early investments are penalized; late investments rewarded (opportunity cost design).
  - *Threshold/custom*: sharp cost change at a breakpoint (MTG's 5-mana cliff).
- **Test for:** Build a balance spreadsheet listing every option with its resource cost and all numerical effects translated to the same unit (DPS, effective HP, resource-gain-per-turn). Sort by cost. Options that produce outlier benefit-to-cost ratios (> mean + 2σ) are above-curve; flag for nerf or cost increase.
- **Failure mode:** *Power creep* — new options must be above-curve to sell / feel rewarding, so each expansion shifts the average upward until old content is obsolete. Mark Rosewater's "Escher Stairwell" mitigation: push power up in one game area per release while pulling it down in others, keeping the global average constant. ⚠️ (from community analysis of Rosewater's statements; not a formal published Wizards methodology)

**Rule 2.5 — Lenticular design encodes complexity that does not overwhelm beginners.**

A single card/option/ability can be simple to a novice (face value) and reveal strategic depth to an expert (hidden conditional power). This lets you widen the viable-options pool without raising the entry barrier. ✅ (Rosewater, "Lenticular Design," magic.wizards.com, March 2014)

- **Exemplar:** *Magic: The Gathering*'s *Black Cat* — a 2-mana 1/1 that discards a card when it dies. To a beginner: a small blocker with a minor death effect. To an expert: a target for recursion loops, discard synergies, and sacrifice engines.
- **Test for:** For each option, write two descriptions: one for a first-time player and one for an expert. If the expert description adds strategic value the beginner description does not mention, the card is lenticular. If the expert description is *the same* as the beginner description (no hidden depth), the option lacks complexity headroom.
- **Failure mode:** *Complexity that only punishes beginners* — advanced interactions that harm novices without rewarding experts (anti-lenticular).

> **Procedural implication:** A generator creating cards, weapons, or units should output a cost-curve score for each item and compare it against the current mean ± tolerance. Items outside 1σ should be adjusted before being placed in pools. For intransitive systems (factions, deck archetypes), the generator must output a payoff matrix and verify the cycle holds before finalizing the faction set. Lenticular properties can be procedurally added by attaching a "conditional upside" modifier to any simple base effect — but verify the conditional does not make the item above-curve when the condition is easy to trigger.

---

### Sub-domain 3: Difficulty Curves and Dynamic Difficulty Adjustment

> **The flow channel as the target:** Csikszentmihalyi's *Flow* (1990) defines a three-state model: challenge >> skill → anxiety/frustration; challenge ≈ skill → flow; challenge << skill → boredom. The designer's job is to keep the player in the narrow flow band while both skill and challenge increase over the game's arc. ✅ (Csikszentmihalyi, *Flow*, 1990; Schell, *The Art of Game Design*, 3rd ed., Lens #18)

---

**Rule 3.1 — Model the difficulty curve as a rising wave, not a flat line.**

Challenge should trend upward but oscillate: brief relief troughs below the current skill level (the "power fantasy moment") followed by rising challenge. The flat-line approach stays in-channel but produces less engagement depth.

- **Exemplar:** *Super Mario Bros.* (1985) — World 1-1's flat ground and single Goomba teaches the jump. 1-2 introduces hazards. 1-3 oscillates with short enemy-dense sections followed by clear platforms. The macro arc rises while micro troughs let players apply learned skills confidently. ✅ (Schell, *The Art of Game Design*, Lens of Flow; cited in gamedeveloper.com Flow Channel article)
- **Test for:** Plot a challenge estimate (enemy HP × count × speed, or equivalent) for every encounter in sequence. The trend line should rise. The variance around the trend should create recognizable peaks and troughs — not a monotonic climb (fatigue risk) and not purely random (unpredictable anxiety spikes).
- **Failure mode:** *Difficulty spike* — a single encounter with challenge >> current skill level, often caused by a new mechanic introduced at full intensity without prior teaching. In single-save high-lethality games, an unannounced spike is catastrophic.

**Rule 3.2 — DDA must define its target metric, its adjustment range, and its update cadence before implementing.**

Dynamic Difficulty Adjustment adjusts game parameters in real time based on measured player performance. Before building any DDA system, answer: what does it measure, what does it change, how fast does it change, and is that visible to the player? ✅ (Wikipedia, Dynamic game difficulty balancing; Gamedeveloper.com, "Game Changers: Dynamic Difficulty")

- **Named DDA techniques** (after Wikipedia DDA article):
  - *Parameter manipulation* (Hunicke & Chapman): adjust weapon damage, enemy HP, item availability directly.
  - *Dynamic scripting* (Spronck et al.): assign probability weights to NPC behavior rules, updated per-encounter.
  - *Reinforcement learning* (Andrade et al.): offline-trained agent adapts online to specific player.
  - *Rubber-band physics* (Mario Kart): item quality and CPU speed tied to player rank in a race.

**Rule 3.3 — The L4D AI Director's "emotional pacing" model is the benchmark for cooperative DDA.**

The AI Director monitors each player's health, stress level, encounter history, and skill signals in real time. It controls: zombie spawn volume, spawn positions, item placement (medkits/ammo), enemy type mix, and dynamic music. Its goal is a *peak / relief / peak* emotional arc — not a fixed difficulty level. ✅ (Valve GDC 2009 presentations by Michael Booth; confirmed by Wikipedia Left 4 Dead entry; Gamedeveloper.com)

- **The Director's state machine:** build tension → sustained intensity peak → mandatory lull (clear area, items available) → build tension again. The lull duration is proportional to how severe the previous peak was.
- **Exemplar:** *Left 4 Dead* (2008) — a four-player team that is playing poorly (low health, many deaths) receives fewer special infected, more medkits, and shorter hordes. A team playing well receives more Hunters and Tanks, fewer ammo drops, and longer sustained hordes. Same level map, wildly different encounter experience. ✅
- **Test for:** Record player health and death frequency per chapter. Plot against encounter intensity (zombie count per minute). If high-death runs also show high intensity without lull insertion, the Director is failing to relieve pressure. If low-death runs never reach a peak, the Director is failing to challenge. Target: every chapter should contain ≥1 clear peak and ≥1 clear lull identifiable in the data.
- **Failure mode:** *Director blindness* — adjusting global parameters without tracking individual player stress, so one struggling player brings the whole team into easy mode while others are underchallenged.

**Rule 3.4 — RE4's hidden difficulty score adjusts on a 1–10 scale based on combat performance.**

Resident Evil 4 (2005) maintains an internal difficulty score. A standard playthrough begins at rank 6. Successful combat (hits landed, enemies killed, damage avoided) raises the score; repeated deaths and missed shots lower it. Higher ranks increase enemy count, aggression, hit points, and damage output; lower ranks reduce them. ✅ (Wikipedia DDA article confirms system; CBR article details mechanics)

- The system's stealth was intentional: players experience a natural-feeling difficulty slope without perceiving the hand of an algorithm.
- **Failure mode:** *Discovered DDA exploitation* — players who learn the system deliberately die early to lock a low rank, then play through on trivially easy settings (documented in RE4 community).

**Rule 3.5 — Visible difficulty meters maintain perceived fairness at the cost of player gaming the system.**

God Hand (2006) displays a skull-shaped meter that increases as the player successfully dodges and defeats enemies (four levels, with the highest labeled "DIE!"). Players always know exactly what difficulty tier they are in. ✅ (Gamedeveloper.com "Game Changers: Dynamic Difficulty"; Wikipedia DDA article)

- Ernest Adams' rule: if you hide the DDA mechanism, players who discover it feel deceived. If you show it, players who are losing may feel the game is mocking them. Neither is wrong — it is a tone and audience decision.
- **Test for:** If DDA is hidden, write a player-facing rationale ("the world responds to you") and verify that the system cannot be exploited by deliberate underperformance. If DDA is visible, verify that the top tier is still completable by skilled players and the bottom tier still teaches core mechanics rather than trivializing them.
- **Failure mode:** *Rubber-band reversion* — DDA that so aggressively tracks player performance that skilled play is immediately negated (e.g., Mario Kart's item system occasionally perceived as unfair because skilled players receive almost no useful items while losing players receive game-turning items).

**Rule 3.6 — Perceived fairness is as important as actual difficulty calibration.**

A game that is mechanically fair but feels arbitrary (surprise deaths, invisible hazards, hidden DDA exploitation) fails the fairness test. A game that is slightly mechanically unfair but clearly communicates the rules and stakes can feel fair. ✅ (Rollings & Adams cited in Wikipedia DDA; Civilization AI cheating example from gamedeveloper.com "Balancing Act")

- **Exemplar:** Civilization (any version) openly discloses that higher difficulty gives the AI production bonuses. Players accept this as fair because *they were told*. A game that secretly boosted AI resources would feel like cheating.
- **Test for:** Can a player accurately predict why they died / lost / were put at a disadvantage? If yes, the game is perceived-fair. If players attribute losses to "the game cheating," the transparency is insufficient.
- **Failure mode:** *Opaque disadvantage* — hidden stat bonuses, unannounced difficulty scaling, or invisible rubber-banding that players cannot attribute to their own play.

> **Procedural implication:** A procedural encounter generator cannot manually tune each encounter. It must attach a challenge estimate to every generated encounter and validate the *sequence*, not just each piece individually. Required gates: (a) no encounter sequence may contain a challenge value > 2.5× the current session-average without a preceding easier encounter that introduces the mechanic; (b) every N encounters must include at least one encounter with challenge < 0.7× session-average (the lull). These are automated constraints, not recommendations. Under single-save high-lethality, an unannounced procedural spike is a game-ending event for the player — the constraint is non-negotiable.

---

### Sub-domain 4: Difficulty Settings and Accessibility

> **Two distinct problems:** Difficulty settings are about matching player preference to challenge level. Accessibility is about removing barriers that prevent play entirely (motor, cognitive, perceptual). These overlap but are not the same. Good design addresses both independently. ✅ (accessibility experts quoted in junkee.com/Sekiro article; gamedeveloper.com/Celeste)

---

**Rule 4.1 — Granular per-axis assists outperform a small number of discrete difficulty modes.**

Discrete modes (Easy / Normal / Hard) bundle multiple difficulty axes (enemy damage, player HP, puzzle complexity, time pressure) that players may need at different levels. Granular assists let players tune each axis independently.

- **Exemplar:** *Celeste* (2018, Matt Thorson / Maddy Thorson) Assist Mode:
  - Game speed: 50% to 100% in 10% steps. ✅
  - Invincibility toggle (on/off). ✅
  - Infinite stamina toggle. ✅
  - Chapter skip. ✅
  - "Assist Mode" was originally named "Cheat Mode"; the rename was intentional to remove shame framing. ⚠️ (Thorson's reason stated in interviews; second source not located as a direct quote)
  - The design principle: *individual axes of challenge* (reaction time → speed slider; survivability → invincibility; stamina management → infinite stamina). Players who struggle with reaction time but enjoy platforming puzzles can slow time without becoming unkillable.
- **Test for:** List the distinct skill axes your game challenges (reaction time, resource management, spatial reasoning, memorization, build complexity). For each axis, is there a corresponding assist that reduces difficulty on that axis *without* nullifying the others? If any major skill axis has no corresponding assist, the accessibility is incomplete.
- **Failure mode:** *Axis bundling* — a single "Easy Mode" that simultaneously reduces reaction windows, enemy damage, and puzzle complexity, making the game trivially easy for a player who only needed help with one axis.

**Rule 4.2 — Difficulty as design intent (the Miyazaki position) is a valid artistic choice with explicit exclusion costs.**

From Software's Hidetaka Miyazaki on Sekiro (2019): "We don't want to include a difficulty selection because we want to bring everyone to the same level of discussion and the same level of enjoyment." The shared overcoming of a fixed challenge is itself the designed experience. ✅ (Steam community post quoting Miyazaki; multiple news coverage of the same quote)

- This is a coherent design position — but it carries a documented accessibility cost: players with motor disabilities, repetitive strain injuries, or situational limitations (playing with a sleeping child on arm) cannot access the game at all.
- **The distinction** (from accessibility researcher half-coordinated): difficulty (relative to player ability) ≠ accessibility (removal of barriers to play). A game can have high difficulty without accessibility barriers; removing barriers does not require reducing difficulty for players who want it.
- **Test for:** If no difficulty assists exist, is the design intent publicly stated and the fixed challenge demonstrably achievable through in-game mechanics without hardware modification? (i.e., is the game actually beatable by its target audience?) If yes, the Miyazaki position is defensible. If the game is "hard" due to poor design rather than intentional design, no difficulty intent justifies the exclusion.
- **Failure mode:** *Difficulty as gate-keeping* — using "design intent" to avoid the engineering work of accessibility, when the actual game is frustrating due to unfixed jank rather than deliberate design.

**Rule 4.3 — The inclusion vs. purity debate is resolvable: optional assists that players choose to use do not affect other players' experience.**

In a single-player game, a player using Celeste's Invincibility Toggle has no effect on any other player's experience. The "it diminishes my accomplishment if others can skip it" argument fails: achievements are personal. The counterargument (Celeste's own documentation) applies only to multiplayer: assists that give one player an advantage over another are a balance problem, not just an accessibility question.

- **Test for:** Do the proposed assists affect other players' experience (multiplayer)? If no → implement without guilt. If yes → scope assists to single-player modes or implement as opt-in lobbies.
- **Failure mode:** *Bleeding accessibility into competitive modes* — applying single-player assists to ranked/competitive modes where they create actual unfairness.

**Rule 4.4 — Naming and framing of difficulty options affect player self-perception.**

"Assist Mode" (Celeste) → neutral / supportive. "Story Mode" (many games) → implied you are not a gamer. "Narrative Mode" (Gears) → reframed as a genre choice. "Easy Mode" → implied inferiority. The label affects whether players use the option. ✅ (multiple Celeste accessibility articles; VICE interview)

- **Test for:** Does the difficulty label imply judgment of the player's skill or worth? If yes, replace with a neutral descriptor that describes what the mode does ("Exploration Mode," "Reduced Enemy Damage," "One-Hit Shield") rather than what it says about the player.
- **Failure mode:** *Shame friction* — players who need an assist do not use it because the label makes them feel inferior, and they abandon the game instead.

> **Procedural implication:** For a procedurally generated game, difficulty settings must be implemented as multipliers on generated parameters (encounter budget, respawn timer, resource drop rate, DDA threshold) rather than separate content generation paths. A separate "easy" content path doubles the content generation requirement and diverges the two experiences. Instead, generate one base experience and apply per-axis scalars. This is also the only way to support granular per-axis assists at scale.

---

### Sub-domain 5: Metrics-Driven Balance — Spreadsheets, Simulation, and Telemetry

> **The three instruments:** (1) Pre-ship *spreadsheet / cost-curve analysis* — mathematical verification before the game runs. (2) Pre-ship *self-play simulation* — automated agents discover dominant strategies faster than human playtests. (3) Post-ship *telemetry* — real player data drives live tuning. All three are necessary; none alone is sufficient.

---

**Rule 5.1 — Build and maintain a balance spreadsheet for every transitive system.**

For each option (weapon, unit, card, ability), record: resource cost, all quantifiable benefits (damage/second, effective HP, resource generated, movement speed), all quantifiable costs (cooldown, reload time, resource consumed). Normalize to a single per-second or per-resource-unit value. Sort by cost. Outliers beyond ±1σ warrant investigation.

- **DPS normalization formula (weapon example):**
  `Sustained DPS = (base_damage / (1/fire_rate + reload_time)) × (1 + crit_chance × (crit_multiplier - 1))`
  All weapons should fall within a ±15% band of the same-tier DPS target. ✅ (gamedev.net RPG systems thread; hitem3d.ai balance guide)
- **Exemplar:** *Diablo 3: Reaper of Souls* balance team used internal DPS spreadsheets to ensure set-item bonus powers scaled differently from base item stats, creating build-enabling outliers (intentionally above-curve) without making them mandatory for basic progression. ⚠️ (widely documented in community; Blizzard has not published the internal spreadsheet methodology)
- **Test for:** Does your balance spreadsheet exist? Does it cover 100% of options in each transitive system? Do any options fall outside ±2σ of the DPS/value curve for their tier? Flag all outliers before each content ship.
- **Failure mode:** *Spreadsheet gap* — options added in expansion content without being added to the balance spreadsheet, creating unchecked above-curve items that dominate until discovered by players post-ship.

**Rule 5.2 — Self-play simulation discovers dominant strategies faster than human playtest, but requires objective design.**

Reinforcement-learning agents trained via self-play can enumerate dominant strategies across a combinatorial option space that human playtesters cannot cover in the same calendar time. The critical design constraint: reward the agent for winning *via diverse strategies*, not just winning. ⚠️ (IEEE 2021 "Toward Automated Game Balance"; arxiv:2503.18748 "Simulation-Driven Balancing"; RuleSmith arxiv:2602.06232 — all active research papers, not validated industrial standard)

- **Exemplar (research-stage):** RuleSmith (arxiv:2602.06232, 2025) uses multi-agent LLMs to iterate on game rules, evaluating each iteration via simulated play for balance metrics. The system flagged dominant strategies in a test RTS within hours that took human playtesters weeks. ⚠️
- **Test for:** If using self-play balance testing, verify that the agent explores all major strategy archetypes, not just the globally optimal one. Use population-based training (diverse agent pool) rather than single-agent self-play to prevent strategy collapse.
- **Failure mode:** *Strategy collapse in self-play* — two self-play agents converge on the same dominant counter-strategy and stop exploring, producing a "balanced" result that is actually a two-option degenerate equilibrium.

**Rule 5.3 — Post-ship telemetry targets are specific and numerical.**

Collect: option pick rate, win rate (per option, per mode), death location heatmaps, boss/level abandonment rate, session length by difficulty setting, and time-to-first-successful-completion per challenge. ✅ (hitem3d.ai; daydreamsoft.com; videogamedevelopmentauthority.com)

- **Action thresholds (industry practice, ⚠️ sourced from practitioner guides, not peer-reviewed studies):**
  - Pick rate ≥75% for a single option in a slot → probable dominant strategy; investigate immediately.
  - Boss abandonment rate ≥60% → tuning emergency; reduce difficulty or add telegraph.
  - Win rate for one faction/character outside 45–60% at high elo/skill → asymmetric balance problem.
  - Session length dropping ≥20% after a specific encounter → probable difficulty spike.
- **Exemplar:** *StarCraft II* — Blizzard's balance team uses tournament win-rate data (from aligulac.com and Blizzard's own ladder statistics) to drive patch timing and scope. A matchup win rate outside 48–52% for the top 200 players triggers a balance review. ✅ (aligulac.com balance report; illiteracyhasdownsides.com analysis of dominant-player effects on race statistics)
- **Test for:** Do you have logging for all listed metrics? Are you computing them per-session, per-patch, and per-skill-bracket? Are action thresholds defined in writing before shipping so the team acts on data rather than opinion?
- **Failure mode:** *Vanity metrics* — tracking total playtime or DAU without per-option or per-encounter breakdowns, yielding no actionable balance signal.

**Rule 5.4 — Single-player and multiplayer balance require different instruments and different objectives.**

Single-player balance target: every player completes the critical path with an experience in the flow channel. The "opponent" is a fixed challenge, so balance means calibrating that challenge to a range of skill levels, not to player vs. player equity.

Multiplayer balance target: all starting options (characters, factions, decks) produce equal expected win rates at matched skill levels. The "opponent" is another human, so balance means ensuring no option creates an inherent advantage independent of skill. ✅ (Sirlin Part 1 definitions; hitem3d.ai comparison)

- **Failure mode (single-player):** *Tuning to the median player* — the challenge is right for the 50th-percentile player but creates a brick wall for the bottom 25% and trivializes content for the top 25%. Fix with DDA or per-axis assists.
- **Failure mode (multiplayer):** *Skill-bracket blindness* — an option is balanced at pro level but dominant at casual level (or vice versa). StarCraft: some unit counters require micro-skill only available at GM level; below that threshold, the "intended counter" doesn't function, making the unit appear unbeatable. Blizzard maintains separate balance targets for ladder brackets and pro play.

**Rule 5.5 — Covert DDA used to drive monetization is an ethical and legal risk.**

EA's 2020 lawsuit alleged that DDA in its sports games was covertly tuned to create frustrating experiences that drove loot-box purchases. The lawsuit was voluntarily dismissed in 2021; EA denied all claims as "baseless." ✅ (Wikipedia DDA article documents the lawsuit and dismissal)

- Regardless of the legal outcome, the design lesson is clear: DDA systems that are tuned against player interests (rather than for player experience) will be discovered, and the reputational damage is severe.
- **Test for:** For every DDA parameter adjustment, can you write a player-facing justification phrased as "this makes your experience better because..."? If the honest answer is "this makes you frustrated so you spend money," the system is misaligned and should be redesigned.
- **Failure mode:** *Monetization-aligned DDA* — adjustments that reduce player satisfaction to create spending pressure. Distinct from and more serious than *rubber-banding*, which merely reduces skill-outcome correlation.

> **Procedural implication:** A procedural game that generates content at runtime must instrument every generated encounter at generation time: attach a predicted challenge score (enemy budget, estimated completion time, expected player health remaining), log it alongside actual outcome data, and feed the delta back into the generator's parameters. This creates a closed-loop auto-tuning system: generated-challenge vs. measured-outcome discrepancy drives parameter updates. This is the only scalable substitute for per-encounter human playtesting in an AI-authored content pipeline. The update cadence must be bounded (no more than ±X% per session to prevent oscillation) and the update range must be capped to prevent the system from disabling itself under extreme player performance.

---

## Recommendations

**Stage 0 (Pre-design) — Declare your balance objectives in writing.**

Before building any system, answer: symmetric or asymmetric? How many distinct options per slot? What is the target skill bracket (casual / competitive / both)? What is your DDA policy (hidden / visible / none)? What are your telemetry action thresholds? Decisions made after content ships are 10× more expensive.

**Stage 1 (System design) — Build the balance spreadsheet before the first asset.**

Map every option to a cost-curve value. Define the DPS/value target for each tier. Define the payoff matrix for any intransitive system. This is a 2-hour spreadsheet that prevents a 200-hour balancing crisis.

**Stage 2 (Alpha) — Run self-play simulations on all competitive systems.**

Before human playtest, run RL agents or scripted bots on all multiplayer / competitive systems. Flag any option with >65% win rate in simulation. Fix or cost-adjust before human players can form opinions.

**Stage 3 (Beta) — Instrument telemetry and define action thresholds.**

Log pick rates, win rates, abandonment rates, death heatmaps. Define thresholds before launch. Assign ownership — who acts on telemetry, how fast, and what the minimum patch process looks like.

**Stage 4 (Post-launch) — Maintain the balance spreadsheet in lockstep with content updates.**

Every new option added to the game must be entered into the spreadsheet before ship. Balance patches must update the spreadsheet retroactively. The spreadsheet is a living document, not a pre-launch artifact.

**Thresholds that change the plan:**

- If the game is purely single-player → drop the asymmetric-faction balance tests; focus all effort on DDA and difficulty curve.
- If the game is live-service multiplayer → promote telemetry to Stage 1; balance patches may ship weekly; treat the balance spreadsheet as the canonical truth that overrides intuition.
- If the game uses procedural content generation → every balance rule becomes an automated pre-generation constraint, not a post-generation review. Budget 20–30% of generator engineering effort for balance constraints.
- If the game has a single-save high-lethality design → difficulty spikes are catastrophic; the challenge-sequence constraint (no spike without prior teaching encounter) is a hard build gate, not a style guideline.
- If the target audience includes casual / accessibility-impaired players → implement granular per-axis assists before difficulty modes, and name them neutrally.

---

## Caveats

1. **Self-play RL for balance is research-stage, not industrial standard (as of 2026).** The papers cited (IEEE 2021, arxiv:2503.18748, RuleSmith 2025) are promising but describe research prototypes. No publicly documented case of a shipped AAA game using RL self-play as its primary balance instrument was found. Treat as a forward-looking technique requiring tooling investment.

2. **Telemetry thresholds (75% pick rate, 60% abandonment) are practitioner heuristics, not empirically validated cutoffs.** They appear in practitioner guides and consulting materials but not in peer-reviewed studies. Treat as starting points for your own calibration rather than universal laws.

3. **RE4's "fear AI" label is community-created.** Primary Capcom documentation does not use this term. The underlying system (1–10 hidden difficulty score) is well-documented and verified, but the "fear AI" branding should not be repeated in design docs as if it were an official Capcom system name.

4. **The EA DDA / loot-box lawsuit (2020) was dismissed voluntarily; EA denied all claims.** It is cited here as a cautionary tale about covert monetization-aligned DDA, not as a finding of wrongdoing.

5. **Rosewater's "Escher Stairwell" and "Power Points Budget" are community-documented, not formally published Wizards of the Coast methodology.** The concepts are consistent with Rosewater's public statements and "Making Magic" column, but no formal design document was located.

6. **Accessibility vs. difficulty is an evolving normative debate.** The framing of "difficulty settings as accessibility" (Celeste camp) vs. "difficulty as design intent" (Miyazaki camp) is a live industry argument without a settled answer. Both positions are documented here; neither is endorsed as the only correct approach. The agent should surface the trade-off to the human designer rather than choosing by default.

7. **AI-authored / procedural content multiplies the surface area for accidental dominant strategies.** The existing literature on dominant strategies and DDA assumes hand-authored content reviewed by humans. A procedural generator can emit thousands of items, encounters, or options per second, any of which could be inadvertently above-curve. The automated invariant approach described throughout this document is the field's best current answer, but it is under-studied in production settings as of 2026.

---

## Sources

- Sid Meier, "Interesting Decisions," GDC 2012. (Escapist coverage: https://www.escapistmagazine.com/gdc-2012-sid-meier-sees-interesting-decisions-even-in-rhythm-games/)
- David Sirlin, *Playing to Win* (2001); "Balancing Multiplayer Games Part 1: Definitions" and "Part 2: Viable Options," Sirlin.net. https://www.sirlin.net/articles/balancing-multiplayer-games-part-1-definitions · https://www.sirlin.net/articles/balancing-multiplayer-games-part-2-viable-options
- Game Balance Concepts blog, Level 3 "Transitive Mechanics and Cost Curves" (2010). https://gamebalanceconcepts.wordpress.com/2010/07/21/level-3-transitive-mechanics-and-cost-curves/
- Game Balance Concepts blog, Level 9 "Intransitive Mechanics" (2010). https://gamebalanceconcepts.wordpress.com/2010/09/01/level-9-intransitive-mechanics/
- Michael Booth (Valve), "Replayable Cooperative Game Design: Left 4 Dead," GDC 2009. PDF: https://cdn.akamai.steamstatic.com/apps/valve/2009/GDC2009_ReplayableCooperativeGameDesign_Left4Dead.pdf · GDC Vault: https://gdcvault.com/play/1422/From-COUNTER-STRIKE-to-LEFT
- Wikipedia, "Dynamic game difficulty balancing." https://en.wikipedia.org/wiki/Dynamic_game_difficulty_balancing
- Wikipedia, "Left 4 Dead." https://en.wikipedia.org/wiki/Left_4_Dead
- CBR, "Resident Evil 4's Most Important Feature Is Its Dynamic Difficulty." https://www.cbr.com/resident-evil-4-dynamic-difficulty-capcom/
- Jesse Schell, *The Art of Game Design: A Book of Lenses*, 3rd ed. (2019). Flow Channel diagram: https://www.researchgate.net/figure/Flow-channel-from-Jesse-Schells-Art-of-Game-Design-A-Book-of-Lenses-A-represents_fig1_317175208
- Gamedeveloper.com, "Game Design Theory Applied: The Flow Channel." https://www.gamedeveloper.com/design/game-design-theory-applied-the-flow-channel
- Gamedeveloper.com, "Game Changers: Dynamic Difficulty." https://www.gamedeveloper.com/design/game-changers-dynamic-difficulty
- Gamedeveloper.com, "Check out Celeste's remarkably granular Assist options." https://www.gamedeveloper.com/design/check-out-i-celeste-s-i-remarkably-granular-assist-options
- Celeste Wiki, Assist Mode. https://celeste.ink/wiki/Assist_Mode
- VICE, "Why The Very Hard Celeste is Perfectly Fine With You Breaking Its Rules." https://www.vice.com/en/article/celeste-difficulty-assist-mode/
- Miyazaki on Sekiro difficulty (Steam Community thread). https://steamcommunity.com/app/814380/discussions/0/1815422173027344395/
- Junkee, "Sekiro: Why Video Game Difficulty Is An Accessibility Issue." https://archive.junkee.com/sekiro-game-difficulty/200666
- Mark Rosewater, "Lenticular Design," Making Magic column, Wizards of the Coast, March 2014. https://magic.wizards.com/en/news/making-magic/lenticular-design-2014-03-31
- aligulac.com StarCraft II balance report. https://aligulac.com/misc/balance/
- illiteracyhasdownsides.com, "How Much Do Dominant Players Affect StarCraft Balance Data?" https://www.illiteracyhasdownsides.com/p/how-much-do-dominant-players-affect
- IEEE Xplore, "Toward Automated Game Balance: A Systematic Engineering Design Approach" (2021). https://ieeexplore.ieee.org/document/9619032/
- arxiv:2503.18748, "Simulation-Driven Balancing of Competitive Game Levels with Reinforcement Learning" (2025). https://arxiv.org/pdf/2503.18748
- arxiv:2602.06232, "RuleSmith: Multi-Agent LLMs for Automated Game Balancing" (2025). https://arxiv.org/pdf/2602.06232
- Machinations.io, "What is Dominant Strategy?" https://machinations.io/glossary/dominant-strategy
- Paradigm Plus, "What is Game Balancing? An Examination of Concepts." https://paradigmplus.itiud.org/volume1/number1/becker/
- hitem3d.ai, "Game Balancing Tips for Fair & Fun Play in 2026." https://www.hitem3d.ai/blog/en-Game-Balancing-How-to-Create-Fair-and-Engaging-Gameplay/
- daydreamsoft.com, "Observability & Telemetry in Live Games." https://www.daydreamsoft.com/blog/observability-and-telemetry-in-live-games-building-data-driven-player-experiences
