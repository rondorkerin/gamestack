# Pacing & the Player Journey — Guide

Engineering engagement across time: the interest curve, tension/rest rhythm, novelty scheduling, retention loops, and the pacing director algorithm. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Jesse Schell's *The Art of Game Design* (Ch. 16, 3rd ed. 2019), Csikszentmihalyi's flow theory (1990) and Jenova Chen's MFA thesis (USC 2006), Michael Booth's L4D AI Director (GDC 2009), Ken Birdwell's Half-Life 2 pacing analysis (GDC 2006), Daniel Brewer's Warframe spawn manager (GDC 2013), Daniel Cook's "Loops and Arcs" (lostgarden.com 2012), and John Hopson's "Behavioral Game Design" (gamedeveloper.com 2001). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round1-universal/U6`).

> **The central law:** *The interest curve is fractal — design it at every timescale simultaneously.* A game whose encounter pacing is excellent but whose session pacing is flat loses players between sessions. A game whose playthrough arc is strong but whose moment-to-moment pacing is a treadmill loses players inside sessions. Both failures share the same shape: unvaried intensity without deliberate contrast.

> For the second-to-second feel of individual actions see `game-feel-and-juice`. For spatial pacing in open worlds see `open-world-design`. For the flow/motivation theory underlying the flow channel see `game-design-fundamentals`.

---

# Sub-domain A — The Intensity / Interest Curve

> Jesse Schell's Lens #62 (*The Art of Game Design*, Ch. 16): a successful interest curve opens with a hook, rises through peaks and valleys of gradually increasing stakes, terminates at a climactic high, and resolves in a brief denouement. Three constituents compound: **inherent interest** (what's at stake), **poetry of presentation** (audiovisual craft amplifying emotional registration), and **projection** (how much the player cares about the outcome). ✅

## A.1 The fractal rule — design the curve at every timescale

The same peak-valley-climax shape that governs a full playthrough also governs a session, a level, and a single encounter. Zoom in and the pattern repeats. ✅ (Schell Ch. 16; multiple design analyses)

- **Exemplar:** *Half-Life 2* (Valve, 2004) escalates at the macro level from City 17 to the Citadel, but each chapter (Ravenholm, Nova Prospekt, Highway 17) has its own local peak. Within Ravenholm, each room has a micro-peak. Valve designers graphed intensity on X=distance vs. Y=0–100% and iterated every scale. ✅ (GDC 2006 presentation; *HL2* postmortem, *Game Developer* 2004)
- **Test for:** At playthrough scale — does the final third contain the game's hardest/most emotionally intense sequence? At session scale — does each 20–90-min play window end on a hook for the next? At encounter scale — does each fight/puzzle have a distinct opening (lower pressure), mid-escalation, and climactic moment?
- **Failure mode — Flat Line:** Every beat registers the same intensity. The player enters a trance and disengages. Common when designers mistake "adding more content" for "varying pacing."

## A.2 The hook rule — earn attention before asking for investment

Open with a moment of high interest in the first 2–5 minutes. Do not delay with character creation, menu tutorials, or unskippable exposition. ✅ (Schell Ch. 16; multiple onboarding analyses)

- **Exemplar:** *Hades* (Supergiant, 2020) drops the player into combat within 30 seconds. *Half-Life* (1998) opens with the tram-ride "Black Mesa incident" before any shooting — immediate sensory engagement before demanding investment. ✅
- **Test for:** Can a new player identify what the game "feels like" within 2 minutes without reading UI text?
- **Failure mode — Dead Zone Open:** Unskippable exposition, character creation, or a long tutorial corridor before any interesting decision. First-impression data shows 73% of mobile players leave within 24 hours ⚠️ (GameAnalytics 2023; mobile-skewed); dead-zone openings front-load that attrition.

## A.3 The climax and denouement rule — land the ending

The final boss/climax must be the game's emotional peak, not necessarily its hardest mechanical challenge. Follow it with a brief falling-action period so players exit feeling resolution, not vertigo. ✅

- **Exemplar:** *Celeste* (Extremely OK, 2018) uses Chapter 7 as the climax — its sub-chapters re-run mechanics from all prior chapters, creating a recapitulation. Chapter 9 ("Farewell") is mechanically harder but emotionally optional, preserving the main arc's landing. ✅ (Giant Bomb analysis; exok.com Chapter 9 postmortem, 2019)
- **Test for:** After the final moment, does the game allow 2–5 minutes of lower-pressure, emotionally resonant content before hard-ending?
- **Failure mode — Anti-Climax:** The final encounter registers lower intensity than a mid-game set-piece. Often caused by designing the final boss for narrative reasons without calibrating its emotional weight. Classic case: *Mass Effect 3*'s ending — mechanically flat, dramatically unresolved. ⚠️

**→ Procedural / headless implication.** A generated experience has no hand-authored moment ordering. The pacing director must score each generated beat's intensity, maintain a target curve function, and reject or reorder content that produces a flat-line or inverted-climax shape. The fractal constraint means the director must operate at all timescales simultaneously — macro (playthrough), meso (session), and micro (encounter). See §F for the algorithm.

---

# Sub-domain B — Challenge & Rest Rhythm

> The psychological mechanism is contrast: intensity is only perceptible relative to calm. RE2 Remake director (Capcom, 2019): "We thought that having nothing but tension throughout the whole game would be exhausting for players, so we designed the save rooms as a safe place to take a breather from the horror." The L4D AI Director formalizes this: after a Peak event, the Director enters a **Relax** phase of ~30–45 seconds before cycling back. ✅ (L4D Director documentation; RE2 Remake developer interviews)

## B.1 The mandatory breathing-room rule

After every high-intensity peak (boss fight, action set-piece, major revelation), guarantee at least one low-intensity beat before the next escalation. The ratio of high:low beats should never exceed 3:1 by duration. ✅

- **Exemplar:** *Dark Souls* (FromSoftware, 2011) places bonfires — calm, friendly-lit rest points with ambient music — after major boss rooms. Firelink Shrine: calm lighting, NPC dialogue, no enemies, gentle music. The contrast makes each fog gate feel genuinely dangerous. ✅ (multiple *Dark Souls* design analyses; theGamesEdge.com "The Safe Room")
- **Test for:** Map every beat in a 30-minute sequence. Does any run of high-intensity beats exceed 10 minutes without a deliberate low-intensity beat? If yes, insert one.
- **Failure mode — Intensity Fatigue:** Sustained high-intensity without rest causes emotional numbing — each successive peak registers weaker. Horror games suffer this worst; action games suffer the "combat treadmill" equivalent.

## B.2 The rest-beat toolbox

The following beat types reliably drop intensity without losing engagement. ✅ (Valve GDC 2006; theGamesEdge.com; Level Design Book)

- **Exploration/travel:** Walking through a safe space, admiring environmental art. (*HL2*: Ravenholm aftermath → Highway 17 drive.)
- **Choreo / NPC dialogue:** Scripted character moment, no enemies. (Valve GDC 2006 categorized HL2 beats as: Explore, Combat, Choreo, Puzzle.)
- **Resource management / inventory:** Organizing loot, crafting, upgrading. The safe-room mechanic.
- **Lore discovery:** Item descriptions, environmental stories. Requires low-stakes space.
- **Vista / environmental payoff:** Arriving at a high point to see the landscape — the Level Design Book identifies vistas as explicit low-intensity beats. ✅

## B.3 The latent-tension rule — rest must carry dread

Rest periods should carry latent tension: the player knows the calm is temporary. Do not signal "you are safe forever." ✅

- **Exemplar:** *Dark Souls* safe rooms carry "a subtle form of dread — every moment of calm is temporary." The bonfire is punctuated by knowledge the fog gate is nearby. *Left 4 Dead*'s saferooms provide literal locked doors and supply caches, but ambient audio of distant zombie moaning maintains tension. ✅ (theGamesEdge.com; L4D Director documentation)
- **Test for:** Do players in playtests visibly relax AND remain alert during rest beats? If they fully disengage (check phones, take long bathroom breaks mid-session), the rest beat carries no tension thread.
- **Failure mode — False Safety:** A rest beat that convincingly signals "all danger is past" breaks the tension curve entirely. Players who feel genuinely safe become bored; re-engaging them requires a harder jolt, which reads as cheap.

**→ Procedural / headless implication.** The pacing director must classify each generated room/encounter by intensity (high / medium / low) and enforce that no high-intensity sequence runs more than 10 minutes without injecting a medium or low beat. The "latent tension" quality of rest beats is harder to automate — it requires environmental art signals (ambient audio, lighting temperature) that must be pre-authored as a style vocabulary the generator *selects from*, not computes at runtime.

---

# Sub-domain C — Novelty Pacing and the Mid-Game Sag

> In a three-act structure, the middle is structurally the most vulnerable: the tutorial's novelty has expired and the climax is not yet in sight. In games, this manifests as a block where the player's skill and the game's content both feel static — nothing new is being taught, nothing new is being rewarded. ⚠️ (design-analysis consensus; no single primary empirical study)

## C.1 The new-toy schedule rule

Introduce at least one new mechanic, system, or content type every 20–40 minutes of designed play. Each introduction should appear first in a safe context (teach), then in a straightforward challenge (test), then in an unexpected variation (twist). ✅

- **Exemplar:** *Portal* (Valve, 2007) never repeats the same exact puzzle — each chamber introduces a new portal application or combination. *Celeste* introduces at least 3 new mechanics per chapter, first in safe rooms, then in structured challenges, then in optional hard extensions. Designer commentary confirms the deliberate anti-staleness intent. ✅ (*Portal* design retrospectives; *Celeste* analysis, pointnthink.fr; Celeste Wikipedia)
- **Test for:** List every distinct mechanic/system. Chart when each is introduced. Is there any gap of more than 40 minutes with no new introduction? That gap is the sag.
- **Failure mode — Mechanic Stagnation:** The player has mastered all systems by hour 2 and spends the remaining playtime applying known tools to known problem types. Common in games that front-load all mechanics in the tutorial.

## C.2 The "new toy must earn its slot" rule

Do not introduce a new mechanic in the final 15–20% of the game. Late introductions feel arbitrary and leave no time for mastery or attachment before the experience ends. ✅

- **Exemplar:** *Game Wisdom* documents the failure: "One of the most annoying things is when a developer just puts something brand new in at the last hour or so of play" — it violates the player's expectation that the late game is a synthesis of mastered skills. ✅ (game-wisdom.com; verified by multiple design posts)
- **Test for:** Are all core mechanics introduced by the 80% mark? If not, classify the late mechanic: (a) power escalation of an existing mechanic (acceptable) or (b) genuinely new system (red flag — cut or move earlier).
- **Failure mode — Late Tutorial:** The final act becomes a tutorial for a new system. The player cannot achieve mastery flow before credits roll; the mechanic is abandoned without payoff.

## C.3 The variety-without-noise rule — reskins are not mechanics

Environmental reskinning (new biome, new enemy faction art) counts as novelty only when paired with a mechanical difference. Pure art reskins sustain interest for ~10 minutes; they do not substitute for mechanical novelty. ✅

- **Exemplar:** *Super Mario Galaxy* (Nintendo, 2007) uses gravity-flip and planetoid mechanics to make each galaxy mechanically distinct. Art changes (ice world, lava world) are always paired with a new physical ruleset. ✅ (Mario Galaxy GDC analyses; Mario design philosophy)
- **Test for:** For each new content zone, list its mechanical differences from the previous zone. If there are none, expect a novelty hit that fades in ~10 minutes.
- **Failure mode — Reskin Fatigue:** "Desert world → Snow world → Lava world" with identical enemy behavior and puzzles. Players progress through visual changes, not mechanical evolution. Common in mid-budget games with high art budgets but limited mechanical investment.

**→ Procedural / headless implication.** A headless pacing director cannot spontaneously invent new mechanics — novelty must be pre-authored as a palette (mechanic A, B, C…) and the director scheduled to introduce each entry at target play-time thresholds. The director must track which mechanics have been introduced and avoid re-introducing already-established mechanics as "new." The generator may inadvertently repeat content types; the director must track recency and enforce a cooling period before a type can be re-introduced as novel. ⚠️ (novel constraint for AI-authored pipelines; not yet in existing pacing director literature)

---

# Sub-domain D — Nested Loops, Retention Ethics, and the Compulsion Line

> ✅ Successful engagement across a playthrough requires designing for four temporal scales simultaneously. (GameAnalytics core-loop documentation; Daniel Cook, "Loops and Arcs," lostgarden.com 2012; mobile live-service research)

## D.1 The four-loop model

| Loop | Scale | Player question | Mechanic type |
|------|-------|-----------------|---------------|
| **Core** | Second–minute | "Does this feel good to do?" | Input responsiveness, immediate feedback |
| **Encounter** | 2–20 min | "Did I win that fight / solve that puzzle?" | Combat/puzzle resolution, tactical decisions |
| **Session** | 20–90 min | "Did I make progress today?" | Milestone completion, resource gain, story beat |
| **Lifetime** | Days–months | "What am I building toward?" | Meta-progression, narrative arc, mastery |

A game only designed at the core-loop level loses players when the first session ends. A game designed only at the lifetime level feels empty moment-to-moment. Weakness at any layer creates an exit point. ✅

- **Test for:** Can you name a distinct payoff type for each of the four loops? If any loop has "same as the loop above," that scale is underdesigned.

## D.2 The loop-arc integration rule

Layer at least one arc (a one-time payoff with narrative or emotional stakes) over every set of gameplay loops. Cutscene-gameplay-cutscene sandwiches are the minimum; looping play with no arc checkpoints produces skilled-but-disengaged players. ✅ (Daniel Cook, "Loops and Arcs," lostgarden.com 2012)

- **Exemplar:** *Hades* (Supergiant, 2020) delivers fresh NPC dialogue every time Zagreus dies — every session loop has an arc payoff (new character dialogue, story beat). The lifetime arc (reaching Persephone) requires ~10 successful escapes; each escape is itself a 30–45-min arc. Meta-progression (permanent unlocks) is the lifetime loop. ✅ (*Hades* design, Supergiant; "Failure is Death and Death is Progress," Medium/Natalia Ahmed)
- **Test for:** After a 20-minute play session that ends in failure, does the player receive at least one arc payoff (story beat, new dialogue, permanent unlock hint)? If the session offers only core-loop satisfaction but zero arc payoff, expect session dropout.
- **Failure mode — Pure Loop Trap:** A mechanically excellent game with zero arc progression. Players enjoy one session then feel no pull to return. Common in pure arcade games and early roguelikes before meta-progression became standard.

## D.3 Appointment mechanics and the ethics line

Appointment mechanics (daily login bonuses, timed resource caps, expiring rewards) should reward attendance positively without punishing absence through loss. Negative appointment mechanics (decaying progress, expiring content) exploit loss aversion and constitute dark-pattern design. ✅

- **Exemplar — ethical:** *Pokémon GO* (Niantic, 2016) gives daily bonus PokéBalls/XP for first catch/spin of the day. Missing a day costs nothing; showing up earns a bonus. *Path of Exile* seasonal leagues expire but do not destroy progress — characters migrate to standard. ✅
- **Exemplar — unethical:** *Game of War* (Machine Zone, 2013) placed countdown timers throughout core systems, creating constant anxiety about expiring resources and requiring return every 2–4 hours or facing competitive disadvantage. FTC investigations later scrutinized similar mechanics. ✅
- **Test for:** For every appointment mechanic: "If a player misses three days, do they lose concrete progress or merely fail to gain a bonus?" Loss = dark pattern. Missed gain = acceptable.
- **Failure mode — Compulsion Spiral:** Variable-ratio reward schedules (loot boxes, randomized gacha pulls) are the highest-activity schedule in operant conditioning but produce compulsive behavior without genuine player agency. Belgium criminalized paid loot boxes (2018); Netherlands followed. The FTC fined Epic Games $245 million (2022) for Fortnite dark patterns specifically targeting children. ✅ (Hopson 2001; Belgium Gaming Commission ruling 2018; FTC settlement 2022)

**The ethics line.** ✅ Mechanics become manipulation when: (1) they exploit loss aversion rather than gain anticipation; (2) they obscure real cost (premium currency obfuscation); (3) they target minors or vulnerable populations; (4) the "reward" requires additional payment to claim. Mechanics are ethical when: players can opt in/out, costs are transparent, no real-money variable-ratio purchases are present, and session-end is never designed to feel like punishment.

**→ Procedural / headless implication.** The lifetime loop in a procedurally generated game requires explicit authoring: the generator cannot spontaneously produce a narrative arc that delivers payoffs across months. The pacing director must track lifetime-loop milestone state (boss kills, story stage, unlock tier) and weight generated content to deliver arc payoffs at appropriate intervals, even when individual sessions are procedurally constructed.

---

# Sub-domain E — First-Hour Pacing and Genre Variation

> "In a world where a player can drop your game and open another in seconds, those first 15–30 minutes are your most important release" (UX Collective, "Games UX," 2023). Day-1 retention averages ~27% across mobile platforms. ⚠️ (GameAnalytics 2023; mobile-skewed — console/PC dynamics differ meaningfully)

## E.1 The hook-before-instruction rule

The player's first meaningful action should deliver a satisfying feedback loop before any significant UI explanation appears. Let the mechanic speak before the manual. ✅

- **Exemplar:** *Portal* (Valve, 2007) places the player in a test chamber with a portal gun before explaining mechanics — discovery is the tutorial. *Doom Eternal* (id, 2020) begins combat within 2 minutes. ✅ (Level Design Book; multiple onboarding analyses)
- **Test for:** In the first 5 minutes, does the player perform the game's core action at least once before reading a tooltip about it?
- **Failure mode — Tutorial Wall:** A 10-minute unskippable tutorial before any meaningful play. The player is positioned as a student before they have any desire to learn.

## E.2 Genre-specific first-hour arcs

| Genre | First-hour shape | Key risk | Exemplar |
|-------|-----------------|----------|---------|
| **Action / FPS** | Fast hook (combat in minutes), then weapon/ability introduction. Low downtime. | Overwhelming with abilities before establishing core feel | *Doom Eternal*: starts with one weapon, adds over 3 hrs |
| **Action-RPG / Souls-like** | Atmospheric arrival, controlled first death teaching core loop, first bonfire payoff | Front-loading stats/build choices before player knows they care | *Elden Ring*: Tutorial crypt → first death → Limgrave arrival vista |
| **Roguelite** | First run is tutorial-run; expect death; teach meta-loop exists immediately after | Permadeath before player understands run structure | *Hades*: first run ends in death cutscene that unlocks a permanent upgrade |
| **Open-world RPG** | Controlled prologue with narrative hook, then "open world reveal" moment (scope shot) | Prologue overstays; player never reaches the scope reveal | *Elden Ring*: elevator scope shot; *Morrowind*: "you are finally free" at Seyda Neen |
| **Puzzle** | Immediate solvable puzzle with intuitive physics; no instruction text | Puzzle 1 is too easy (boring) or too hard (demoralizing) | *Portal*: Chamber 00 trivially solvable in 10 seconds — pure affordance discovery |
| **4X / Strategy** | Tutorial scenario with constrained choices; "first city placed" moment | Analysis paralysis before first positive feedback | *Civilization* "one more turn": first productive action within 2 minutes |
| **Narrative / Walking Sim** | Story hook within first 5 minutes; player agency (even minor) within 10 min | Passive movie for 15+ minutes before any input | *Firewatch*: immediate dialogue choice in prologue |
| **Deck-builder Roguelite** | Tutorial run with curated draft; first boss gated at ~15 min | Card complexity before investment in characters | *Slay the Spire*: 3-act structure, first boss at ~15 min into first run |

✅ (multiple genre analysis sources; BG3/NWN comparison; Level Design Book; Apple Developer onboarding guidelines)

## E.3 The genre-mismatch failure

Do not apply an FPS first-hour arc to an RPG — specifically, do not deploy RPG's extended character-creation sequence in a genre where players expect immediate agency. ✅

- **Exemplar:** *Neverwinter Nights* (2002) required ~20 minutes of character creation before first combat. *Baldur's Gate 3* (Larian, 2023) offers a pre-built character option and drops players into combat (the nautiloid sequence) within 3 minutes. ✅ (design retrospectives; BG3 onboarding analyses)
- **Test for:** What is the player's first input? What is the first satisfying output of that input? How many minutes apart are they?
- **Failure mode — Genre Mismatch Pacing:** An action game's combat blitz opening for a strategy game, or a strategy game's slow build for an action game. Mismatches attract audience and immediately disappoint them.

**→ Procedural / headless implication.** The first hour is the highest-risk segment for generated content: the generator has no equivalent of "hand-authored key scenes." The first hour must contain at minimum: (1) one hand-authored opening hook sequence (not generated), (2) a controlled first generated encounter guaranteed to be winnable (intensity ≤ 30% of max), (3) a guaranteed arc payoff within the first 20 minutes. The pacing director should treat the first hour as a **protected mode**: override its normal intensity budget to enforce this sequence.

---

# Sub-domain F — The Procedural Pacing Director

> The L4D AI Director (Valve, 2008) is the canonical baseline: "Adaptive Dramatic Pacing" designed to create genuinely distinct replays by tracking per-player emotional intensity rather than static difficulty parameters. Michael Booth, GDC 2009. ✅ The Warframe spawn manager (Digital Extremes, GDC 2013) extends this to fully procedurally generated tile sets with no fixed spawn points. ✅

## F.1 The L4D four-phase cycle (canonical)

1. **Build Up** — Director increases enemy pressure ahead of players; ambient tension rises through music and environmental cues.
2. **Peak** — Survivors at maximum emotional intensity. Infected spawning reaches maximum then halts. Panic events (Special Infected, Tank) occur here.
3. **Relax** — Post-peak window of ~30–45 seconds. Director backs off spawning. Players recover, use medkits, regroup.
4. **Dead Time** / Return to Build Up — Survivors regroup; cycle restarts.

The Director tracks per-player **emotional intensity** (scalar: += on damage taken, on killing close-range enemies; -= time since last combat × decay rate) rather than health directly. Design insight from Counter-Strike playtesting: "constant relentless action is wearing — but too many long slow gaps bore the player. The solution is unpredictability." ✅ (GDC 2009 talk; L4D wiki; ausgamers.com GDC 2009 coverage)

## F.2 The Warframe spatial extension

Warframe generates tile sets procedurally (no fixed spawn points). Daniel Brewer, GDC 2013: ✅
- **Tac Map**: Tracks player distance from objective. Enemy strength/quantity slowly increase as players approach completion — macro escalation curve in generated content.
- **Influence Map**: Tracks player line-of-sight through procedurally tiled corridors. Enemies spawn in front of the player, reading influence variables as players move into new zones.
- **Intensity function**: enemies killed + damage taken by players, normalized by player level. Intensity rises → spawning increases → at peak, spawning backs off → relax window enforces.
- **Key insight**: "The usual level design tricks won't work. Instead, procedural design requires intelligent use of metrics to help the AI deal with player behavior to create the illusion of crafted level design." ✅

## F.3 The generalized pacing director algorithm

Drawing from L4D, Warframe, and academic work (PaceMaker, arXiv 2408.15001; AI-directed PCG, Frontiers VR 2026): ✅

```
PACING DIRECTOR — generalized pseudocode (design spec, not engine code)

State machine with four phases: BUILD_UP | PEAK | RELAX | DEAD_TIME

Inputs (tracked per player/squad):
  - intensity_score: float [0..1]
      += on_damage_taken(amount / player_max_hp)
      += on_enemy_killed_nearby (< 10m)
      -= time_since_last_combat * decay_rate
  - distance_to_objective: float [0..1]  (0=start, 1=end)
  - time_in_current_phase: seconds

Phase transitions:
  BUILD_UP → PEAK      when intensity_score > 0.85 OR time_in_build_up > 240s
  PEAK     → RELAX     when intensity_score < 0.7  AND time_in_peak > 15s
  RELAX    → DEAD_TIME when time_in_relax > 35s
  DEAD_TIME→ BUILD_UP  when time_in_dead_time > 20s

Phase effects on content generator:
  BUILD_UP:  spawn_budget = lerp(min, max, intensity_score)
             novelty_pressure = HIGH (eligible to introduce new content types)
  PEAK:      spawn_budget = max; inject panic_event if not recently used
             music = high-tension stem
  RELAX:     spawn_budget = 0 or minimum ambient
             gate loot/reward spawns here
             music = low-tension stem
  DEAD_TIME: spawn_budget = 0; NPC chatter / ambient audio only

Macro overlay (objective distance):
  base_difficulty = lerp(difficulty_min, difficulty_max, distance_to_objective)
  All spawn quantities scaled by base_difficulty * phase_multiplier

Override — First-hour protected mode:
  Force sequence: DEAD_TIME(open hook) → BUILD_UP(intro encounter, cap intensity 0.5)
                  → RELAX(guaranteed reward) → BUILD_UP(normal)
```

- **Genre calibration:** Roguelite run (20–45 min): shorten relax to 15–20s; build-up to 90s; single climax at 80–90% of run. Open-world RPG session (90+ min): extend relax to 60–90s; allow 10-min dead-time exploration windows; reset arc payoff every 20 min.
- **Distance vs. time:** Prefer distance-based pacing for generated traversal content — *HL2* designer Ken Birdwell: "All content is distance-based, not time-based — if players want more action, they move forward." Time-based systems punish fast or slow players. ✅ (Valve GDC 2006; HL2 postmortem)
- **Test for:** Instrument five playthroughs. Does the intensity_score stay below 0.85 for more than 5 continuous minutes? (Director not building up.) Does it stay above 0.7 for more than 10 continuous minutes? (No relax enforced.) Flag both.

## F.4 Spatial pacing for open-world generators

In generated open worlds, use zone density rather than a single intensity timeline. Map the world as concentric rings of escalating difficulty/intensity around objective nodes; sparse outer areas provide breathing room; dense inner areas provide momentum. ✅

- **Exemplar:** *Breath of the Wild* (Nintendo, 2017) distributes Sheikah Towers and shrines at intervals ensuring players always have a visible next goal. *Sleeping Dogs* (United Front, 2012) developers built custom route-analysis tools ("their own version of Google Maps") to examine player pathways before finalizing content distribution. ✅ (Joel Vidqvist BotW thesis, Theseus 2021; gamedeveloper.com "How to Control the Pacing of an Open World Game")
- **Test for:** In a 20-minute free-roam session, does the player encounter at least one high-intensity beat, one medium, and one low? Is there any 10-minute stretch of only high-intensity or only low-intensity?
- **Failure mode — Density Flatness:** Generated open worlds that distribute content evenly produce no sense of escalation or respite. BotW's landmark density is the antidote; even with free exploration, the map communicates gradient.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Flat Line | Every beat same intensity; trance → dropout | Design peaks and valleys at every timescale (A.1) |
| Dead Zone Open | Unskippable tutorial before any agency | Hook within 2 min; mechanic before tooltip (A.2) |
| Anti-Climax | Finale weaker than mid-game set-piece | Score climax as emotional peak, not hardest challenge (A.3) |
| Intensity Fatigue | Emotional numbing; horror stops scary | High:low beat ratio ≤ 3:1 by duration (B.1) |
| False Safety | Rest beat signals "danger is permanently past" | Rest beats carry latent tension / ambient dread (B.3) |
| Mechanic Stagnation | All systems mastered by hour 2 | New mechanic every 20–40 min; teach-test-twist (C.1) |
| Late Tutorial | Final act teaches a new system | All core mechanics introduced by 80% mark (C.2) |
| Reskin Fatigue | Visual zone changes with identical behavior | Art changes paired with mechanical difference (C.3) |
| Pure Loop Trap | Great core loop; no arc payoff; no return | Layer one arc over every loop cluster (D.2) |
| Compulsion Spiral | Loss aversion exploitation; variable-ratio purchases | Appointment mechanics reward gain, never punish absence (D.3) |
| Tutorial Wall | 10+ min unskippable tutorial before play | Core action before first tooltip (E.1) |
| Genre Mismatch | FPS blitz opening for strategy game | Match first-hour arc to genre expectations (E.3) |
| Density Flatness | Open world with evenly distributed content | Zone density gradient; landmark pacing (F.4) |

---

## Caveats

- **L4D timing is secondary-sourced.** The Relax phase "~30–45 seconds" is documented in secondary wiki sources and GDC recaps, not a published primary technical paper. Treat as a genre-specific guideline (cooperative horror shooter) requiring calibration for other genres. ⚠️
- **Flow channel is not real-time measurable.** Csikszentmihalyi's flow channel was derived from experience-sampling studies. The pacing director's intensity_score is a proxy — a useful operational approximation, not a direct flow measurement. Calibrate through playtesting (biofeedback sensors exist in research contexts but are not standard production tooling). ⚠️
- **Day-1 retention data is mobile-skewed.** The 73% day-1 drop-off figure reflects the mobile free-to-play ecosystem; premium PC/console games have meaningfully different first-hour dynamics (players have paid; sunk-cost motivation to continue). Apply mobile urgency as direction, not literal threshold. ⚠️
- **Ethics law is jurisdiction-specific.** Belgium/Netherlands (2018) classified paid loot boxes as gambling; FTC Section 5 enforcement (US) is case-by-case; UK, EU, Korea, and Australia have distinct positions. The ethics line here reflects broadly agreed principles, not a unified legal standard — consult legal counsel for any game with variable-ratio real-money mechanics. ✅
- **Procedural pacing directors are understudied in true open-world contexts.** L4D and Warframe are both mission-based. Open-world generation without a defined "path to objective" requires spatial pacing techniques underrepresented in primary literature; the spatial rules in §F.4 draw primarily from post-release design analyses, not documented developer intent. ⚠️

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
