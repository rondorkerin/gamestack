# Onboarding & Teaching — Guide

The discipline of teaching any mechanic without a pop-up: structured play that builds understanding one concept at a time, in order, at the moment of need. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Koichi Hayashida / Miyamoto's *kishōtenketsu* framework (Nintendo, gamedeveloper.com 2015), the Portal developer commentary (Valve GDC 2008; Combine OverWiki), Celia Hodent's *The Gamer's Brain* (CRC Press, 2017) and GDC 2016 "The Gamer's Brain, Part 2", Daniel Cook's skill atom / loops-and-arcs framework (Lostgarden, 2007/2012), the Heliyon 2022 implicit-tutorial pilot study (PMC9676530), Green et al. procedural tutorial generation (arXiv 1807.06734), and mobile FTUE benchmarks (Mistplay / Segwise / GameAnalytics, 2023–2024). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round1-universal/U3`).

> **The central law:** *teach one mechanic at a time, consequence-free on first contact, gated for mastery on second, and never front-load.* Working memory caps at ~3 novel items simultaneously; exceed it during instruction and the mechanic goes untaught even if displayed. Instruction embedded in a loop sticks; instruction delivered as a pre-game arc does not.

> For reward/flow/motivation theory underneath — skill atoms, the flow channel — see `game-design-fundamentals`. For teaching through spatial layout see `level-design`. For the early difficulty ramp see `difficulty-and-balancing`. For tutorial UI and feedback overlays see `ui-ux-and-feedback`. For first-hour pacing see `pacing-and-the-player-journey`.

---

# Sub-domain A — The Four-Beat Teaching Pattern (Introduce → Test → Combine → Twist)

> Nintendo's Koichi Hayashida formalized the structure as *kishōtenketsu*: introduction (ki) → development (shō) → twist (ten) → conclusion (ketsu). Independently documented across SMB 1-1 (1985), Portal (2007), Celeste (2018), and Super Mario 3D World (2013). ✅ (Hayashida via gamedeveloper.com 2015; Miyamoto via MCV/DEVELOP 2015; Valve Portal developer commentary 2007.)

> **The core constraint:** never combine mechanics the player has not individually mastered. Complexity is a multiplier, not an addition; the combination beat multiplies two fluent skills into one new compound skill.

## A.1 Introduce each new mechanic in a consequence-free space before staked encounters use it

The safe space must carry no loss condition — or a trivially reversible one — so all attention is on observing the mechanic rather than surviving. ✅ (*SMB 1-1*, Miyamoto 1985: the first pit has a filled-in bottom so players cannot die. The open, lethal pit immediately follows. Players who explored the safe pit carry the jump-length model into the lethal one. Source: nocontextculture.com 40th anniversary analysis; Miyamoto via MCV/DEVELOP 2015.)

- **Exemplar:** *Hollow Knight* Crossroads opening (Team Cherry, 2017). A ground dip that cannot kill teaches jumping; a breakable wall teaches attacking; the first Crawlid appears in a wide corridor where flanking is impossible. Source: indiegameculture.com "Hollow Knight's Tutorial Is A Design Masterclass."
- **Test for:** Can a player who has never seen this mechanic encounter its first appearance without taking damage or losing progress? If no, move the introduction earlier or add a buffer space.
- **Failure mode — "Ambush introduction":** the first time a mechanic is needed it is also immediately lethal. Player dies, attributes it to bad luck rather than a lesson, and forms no mental model.

## A.2 Follow the safe introduction with a staked test solvable only by the just-taught mechanic

The gate must have no alternate path so that success is unambiguous evidence of understanding, not luck. ✅ (*Portal* Test Chamber 01, Valve 2007: after players see themselves through the first portal, the next chamber requires walking through one — no other path. Robin Walker: "This puzzle requires walking through a minimum of five portals in a specific order" to prevent accidental completion. Source: Combine OverWiki developer commentary.)

- **Test for:** Can the player clear this segment without executing the target mechanic? If yes, tighten the skill gate.
- **Failure mode — "Bypassed gate":** the test segment has an unintended alternate solution. In Portal, a playtester bypassed a box-button puzzle by portaling the box directly through; a glass barrier was required to close the bypass. (Chet Faliszek, developer commentary.)

## A.3 Introduce a combination scenario requiring two or more previously mastered mechanics

Each mechanic in the combination must have individually cleared its own safe introduction and staked test before appearing here. ✅ (*Celeste* Chapter 1, Thorson / Berry 2018: after separately teaching climbing and dashing, the game presents a crumbly wall that requires dashing into it — the first combination beat. Source: Medium/@josephdiamond; celeste.ink wiki. *Mega Man*, Capcom 1987: each boss weapon is effective against the next boss; stage enemy placement is tuned to the weapon acquired from the prior boss, making acquisition order a soft dependency. Source: daverupert.com.)

- **Test for:** List every mechanic required to solve the combination room. Are all of them independently gated and passed in prior beats? Any orphaned prerequisite is design debt.
- **Failure mode — "Premature combination":** player is asked to combine mechanics before any are individually fluent. Common in strategy games that introduce unit A, unit B, and synergy all at once — player encodes none of the three.

## A.4 Deliver a twist that recontextualizes mastery, then a conclusion that lets the player demonstrate it

The twist is the *same mechanic* applied in a way the player did not predict — not a new mechanic. It should produce an "aha" moment, not confusion. ✅ (Hayashida: "something crazy happens that makes you think about it in a way you weren't expecting — then you get to demonstrate, finally, what sort of mastery you've gained over it." *Portal*'s Companion Cube / incinerator: players build attachment to the cube, are forced to incinerate it, then use the incinerator against GLaDOS. "A nice dramatic payoff" — Jeep Barnett, developer commentary. Source: Combine OverWiki.)

- **Test for:** Does the twist require only understanding from the earlier safe introduction, not new information? Does the conclusion provide a visible, unambiguous success signal (door opens, enemy dies, puzzle clears)?
- **Failure mode — "False twist":** the twist is actually a new mechanic without its own safe-space phase, disguised as a surprise. Player cannot complete it without external help.

**→ Procedural / headless implication.** Given a mechanic definition and its prerequisites, the four-beat template is parameterizable: (a) generate a no-enemy, no-timer safe room using only the target mechanic; (b) generate a gated room solvable exclusively by that mechanic; (c) generate a combination room requiring the new mechanic plus the most recently mastered prerequisite; (d) generate a room applying the mechanic in an inverted or unexpected direction. The hardest constraints to auto-validate are that the safe room contains no accidental loss condition and that the gated room contains no bypass — both require playtesting or the Green et al. ability-ablation test (arXiv 1807.06734): ablate the target mechanic and confirm the level fails without it.

---

# Sub-domain B — Show-Don't-Tell: Teaching Through Level Design and Consequence

> "Show, don't tell" means mechanics are communicated through environmental design, visual affordances, consequence, and player observation — not text pop-ups or forced cutscenes. Academic term: *implicit tutorial*. ✅ (Heliyon 2022 PMC9676530: implicit tutorials rated 0.6/5.0 less boring than explicit; gamedeveloper.com "Methods of Creating Invisible Tutorials"; Valve Portal developer commentary 2007.)

> **The testable condition:** the game must be completable by a player who reads nothing. If text is the primary teaching channel, the level design has failed.

## B.1 Design every mechanic's first appearance so a player who reads nothing can discover it through action and observation

If text must be displayed to make a mechanic understandable, the level design has failed to communicate it. Text may reinforce; it must not substitute. ✅ (*Portal* Test Chamber 00, Kim Swift: "We deliberately positioned this first portal to ensure that players will invariably see themselves" through it. Playtesting revealed players rarely look up without prompting — pistons and ladders were added to force upward attention. Source: Combine OverWiki. *Breath of the Wild* Great Plateau, Nintendo 2017: terrain so elevated that jumping down means death creates a natural fence; every shrine is in visual line-of-sight; Hyrule Castle's storm cloud establishes the long-term goal without a cutscene. Source: uxdesign.cc Javier Miguelez; eliterev.wordpress.com.)

- **Test for:** Playtest with a player instructed to skip all popups and press no buttons until they decide to on their own. Do they correctly discover the core action within 60 seconds of first encountering a new mechanic? If not, the environmental cue is insufficient.
- **Failure mode — "Text dependency":** the mechanic is physically present but not learnable without reading an adjacent popup. When players skip it (and research shows they will), the mechanic is invisible, causing a difficulty spike the designer did not intend.

## B.2 Use affordances to signal mechanic function visually before the player interacts

Affordances are visual cues that communicate use: spikes signal danger, coins signal collection, shiny surfaces signal interactivity, glowing objects signal importance. ✅ (*SMB 1-1* Goomba: moves toward Mario from the right; players instinctively move right; the encounter is unavoidable; the Goomba's eyes and movement signal hostility without a label. Source: nocontextculture.com. *Portal* surface affordances: "round objects and sharp objects" guided attention toward interactive surfaces — Paul Graham, developer commentary; orange-portal color was misread as exit-only, requiring a forced orange-entry chamber to break the misconception. Source: Combine OverWiki — Kerry Davis.)

- **Test for:** Remove all text from the tutorial area. Can a new player identify which surfaces are interactive, which are dangerous, and which are background? Run a 10-person first-look study; count failures.
- **Failure mode — "False affordance":** a design element looks interactive but is not, or vice versa. Creates a mental model mismatch that must be actively unlearned. Fix: a forced-contact chamber that demonstrates the correct model.

## B.3 Use the demonstrative method for mechanics that are lethal or irreversible on first contact

Show the mechanic operating on a non-player target before requiring the player to engage. ✅ (*Half-Life 2* Barnacle, Valve 2004: a Barnacle eats a crow before the player encounters one — teaches the mechanic without player cost. Source: gamedeveloper.com "Methods of Creating Invisible Tutorials." *Dark Souls*, FromSoftware 2011: the asylum demon breaks a door in a cutscene, showing its scale and attack style before combat. Source: game-wisdom.com.)

- **Test for:** List every mechanic that can cause instant loss. Does each have a demonstrative moment — mechanic operating on the environment or an NPC — before the player must engage?
- **Failure mode — "Blind introduction":** the first time a mechanic is seen, the player is already inside it. Classic case: timed disappearing platforms with no preview of the timing cycle; players fall three times before understanding the cycle is regular.

## B.4 Teach through consequence, not through warning

A player who experiences a negative consequence and understands its cause learns more durably than one who is told the consequence will occur. ✅ (*SMB 1-1* mushroom-vs-Goomba: the first mushroom bounces off a pipe and chases Mario at Goomba speed; new players flee it, then catch it by accident and learn it is a power-up through the experience of confusion and resolution, not a label. Source: Miyamoto via blog.adafruit.com; nocontextculture.com. *Spelunky*, Derek Yu 2012: every death has a single clear cause — spike, enemy, arrow trap. The roguelite loop is the tutorial. Source: gamedeveloper.com Spelunky analysis.)

- **Test for:** After every failure moment in the tutorial area, can the player articulate within 5 seconds what caused the failure? If not, causality is not clear enough to teach.
- **Failure mode — "Opaque death":** player fails but cannot identify the cause. Common with overlapping hitboxes, off-screen hazards, or damage-over-time with no visual indicator. Player forms no mental model; they just avoid the area.

**→ Procedural / headless implication.** When generating a level that introduces a procedural mechanic, the generator must produce: (a) at least one interactive object with an affordance matching the mechanic's function (a pressure plate that looks depressible, not a flat floor tile); (b) a demonstrative sequence if the mechanic is lethal; (c) a consequence chain where first-attempt failure has a visible single cause. If the mechanic description includes `affordance_visual`, `demonstrable_target`, and `failure_consequence_visible` fields, all three are auto-validatable before the level reaches a player. Any mechanic whose affordance cannot be expressed in the game's existing visual language requires hand-authoring.

---

# Sub-domain C — Progressive Disclosure & Scaffolding

> Daniel Cook's skill atom framework (Lostgarden, 2007/2012): skill atoms are discrete feedback loops; players can only process atoms they have prerequisites for; introduction must follow the topological sort of the dependency graph. ✅ Csikszentmihalyi's flow channel (1990) applied to games by Koster (2004): challenge must scale with demonstrated skill or the player enters boredom (underchallenge) or anxiety (overchallenge). ✅ Cook's loops-and-arcs distinction: front-loaded tutorials are pure arcs (one-way information delivery, forgotten rapidly); skill-based onboarding embeds teaching inside loops (repeatable feedback cycles where the skill is exercised).

## C.1 Introduce no more than one genuinely new mechanic per level segment

"One new thing per beat" is the operative rule. A new enemy type, a new platform behavior, and a new power-up are three mechanics; all three in one segment exceed working memory and teach none of them. ✅ (*Celeste* chapter structure: one new movement mechanic per chapter — dash in Chapter 1, wall-jumps in Chapter 2, dream blocks in Chapter 3. Each chapter's levels combine only the current chapter's new mechanic with all previously mastered ones. Source: Medium/@josephdiamond; celeste.ink wiki. *Portal* puzzle sequencing: "This puzzle introduced too many new concepts at once, which frustrated playtesters" — Chris Chin, developer commentary; two introductory chambers were inserted before it. Source: Combine OverWiki.)

- **Test for:** Build a mechanic introduction timeline. Count new mechanics per level. Any level introducing more than one new mechanic requires justification; more than two is almost always a design error.
- **Failure mode — "Feature dump":** strategy and RPG games that unlock crafting, skill trees, faction management, and economy in the first 30 minutes. Players learn none of them. MMORPG tutorials are the canonical case.

## C.2 Deliver each mechanic's tutorial at the moment of first need (just-in-time), not pre-emptively

Pre-emptive tutorials are arcs (Cook): one-way information deliveries forgotten by the time they are needed. Just-in-time instruction is embedded in the loop where the mechanic will be used. ✅ (*Breath of the Wild* shrine introductions: each shrine's opening room contains the new rune ability, unlocked and immediately usable before the player exits. The shrine is the tutorial; the tutorial is the game. Source: uxdesign.cc; eliterev.wordpress.com. Fortnite's harvesting weak-point UI, Hodent: the system initially failed because players overlooked it in the tutorial — made it a skill-tree reward instead, shown only after a milestone, as "a meaningful reward with more fanfare." Source: Hodent, Medium/ironsource-levelup.)

- **Test for:** For each mechanic introduced in the tutorial, measure the gap between instruction and first required use. If it exceeds 5 minutes of play, the player has likely forgotten the instruction. Move it closer to use, or add a just-in-time contextual reminder.
- **Failure mode — "Frontloaded dump":** a 10-minute unskippable tutorial before the player can touch the main game. Retention of any individual mechanic is near zero; player frustration is high. (NeoGAF "Forced tutorials in Modern Gaming.")

## C.3 Build a mechanic dependency map and enforce its topological sort as the introduction order

Mechanic B cannot be introduced before A if B requires A to execute. Map dependencies explicitly; treat violations as hard errors. ✅ (Cook's skill chains, Lostgarden 2007: "multiple atoms link together in skill chains — directed graphs showing how mastered skills enable new ones." *Mega Man* boss order: weapon effectiveness communicates the intended order; players who encounter bosses out of order experience the game as unfairly difficult because the dependency is real, only its communication varies. Source: daverupert.com.)

- **Test for:** Draw the dependency graph. Topologically sort it. Does the game introduce mechanics in that order? Any violation is a potential "locked out" moment.
- **Failure mode — "Prerequisite skip":** game introduces Mechanic C, which requires B, which requires A, but the player has only seen A. Player cannot execute C, does not understand why, and experiences it as the game being broken.

## C.4 Show the player the lock before giving them the key

Tease future mechanics visually to create motivation for learning prerequisites. ✅ (Hodent, GDC 2016: "Show the locks before giving the keys. The locks will tease and motivate the players to figure it out." *BotW* Great Plateau: from the starting point, Link can see the entirety of Hyrule — dozens of mechanics and locations, all visible but inaccessible. Source: gamerant.com; uxdesign.cc. *Hollow Knight*: an unreachable area above the opening signals return-later exploration. Source: indiegameculture.com.)

- **Test for:** Does the tutorial area contain at least one visible element the player cannot yet access? Does the mechanic that unlocks it appear within 15 minutes of play?
- **Failure mode — "Invisible future":** the tutorial area is self-contained with no visible content beyond it. Player has no motivation to learn current mechanics except abstract progress. Common in mobile games with a gate between tutorial and main content.

**→ Procedural / headless implication.** The mechanic dependency graph (C.3) is the generator's primary input. Given M = {m₁ … mₙ} with dependencies D (a DAG), the tutorial generator must produce content in topological order. "Locks" can be auto-placed as unreachable geometry or locked objects in the introduction level for any mechanic beyond current introduction scope — a mechanical rule requiring only the mechanic list, not hand-authoring. Verifying that no level contains a prerequisite gap requires the ability-ablation test from Green et al. (arXiv 1807.06734): ablate the target mechanic from the agent's capability set and confirm the level fails without it.

---

# Sub-domain D — FTUE & Early Retention: The First Session, the First Hour

> FTUE (First-Time User Experience) is the design zone covering a new player's initial session. In mobile/F2P it is a measurable conversion funnel with step-by-step drop-off. In premium games it determines whether a player returns for session 2. ✅ D1 mobile retention averages 28–33% (Mistplay / Segwise 2023–2024); D7 drops to 8–13%; D30 to ~6%. Most loss concentrates in or immediately after the tutorial.

## D.1 The player must experience genuine fun within the first 2–3 minutes

The core value proposition of the game must be playable immediately. Players who do not understand the game's fun within 2–3 minutes are statistically very likely to churn. ✅ (*Fortnite*, Epic 2017: the glider mechanic is the first player action in every match — no mandatory credits, no account wall. The core chaos is the first moment. Source: Hodent, Medium/ironsource-levelup. *Candy Crush Saga*, King 2012: first playable move within 5 seconds of launch. Source: GameAnalytics FTUE guide.)

- **Test for:** Timestamp the first moment of genuine player-controlled, in-genre play. Is it under 180 seconds from app launch? Every 30 seconds beyond that costs measurable D1 retention.
- **Failure mode — "Pre-fun gauntlet":** account creation, unskippable cutscenes, terms of service, and forced tutorial precede any gameplay. Players who abandon here are invisible in most analytics — they leave before event tracking fires.

## D.2 Instrument the tutorial as a funnel and fix the drop step before launch

Every tutorial step where a player can exit is a funnel step. Identify the single highest drop-off point — that step has a design flaw. Fix it, re-test, repeat. ✅ (GameAnalytics FTUE guide: "Create a tutorial funnel by creating events for different steps in the tutorial... you will be able to detect the main flaws." Segwise 2023: a seamless, engaging FTUE can increase D1 retention by up to 50%.)

- **Test for:** Pre-launch: run a 20-person recorded playtest and build the funnel manually. Post-launch: instrument with analytics on day one. Identify the single step with the largest drop and fix it.
- **Failure mode — "Uninstrumented tutorial":** game launches without event tracking on tutorial steps. Designer cannot identify where players leave. Iteration is impossible without this data — discovered only after D1 is already too low.

## D.3 Ensure the player experiences a small, clear win before the first obstacle

Psychological need for competence (Cook, Koster, Hodent all converge): players who feel capable continue playing. An early win creates the emotional safety net for the first failure. ✅ (*SMB 1-1* coin boxes: the first coin block is unavoidable and immediately rewarding. Miyamoto: "When they see a coin, it'll make them happy, and they'll want to try again." Source: blog.adafruit.com; nocontextculture.com. GameAnalytics: "Award premium currency for tutorial completion; ensure victories occur during tutorials.")

- **Test for:** In the first 5 minutes, does the player succeed at something with clear visual/audio feedback before encountering their first failure? Note facial response at first success vs. first failure in playtest.
- **Failure mode — "Cold open failure":** game begins with a difficulty spike or a death-eligible encounter before any success moment. Players attribute failure to the game being unfair, not to a lesson to learn.

## D.4 Establish a long-term goal within the first session, visible but not immediately reachable

Without a long-term anchor, players who exhaust short-term motivation have no reason to return. The goal must be visible enough to create anticipation. ✅ (*Breath of the Wild*: Hyrule Castle is visible from the Great Plateau's summit — the entire game's goal, shown in the first minute, completely inaccessible. Source: gamerant.com; uxdesign.cc. *Hades*, Supergiant 2020: Zagreus's escape goal is established in the first run, which he fails — visible goal plus narrative motivation creates the return loop. Source: christi-kerr.com.)

- **Test for:** Can a player who has played 20 minutes articulate a single long-term goal not achievable this session? If not, the game has retention risk at the session boundary.
- **Failure mode — "Goalless tutorial":** teaches mechanics but establishes no narrative or strategic direction. Common in mobile games whose core loop has not been connected to a meta-layer.

**→ Procedural / headless implication.** The first-session arc must be scaffolded at a meta-level even in generated games: (a) the generator must ensure the player's first room/level is winnable and contains an early-win token; (b) the long-term goal must be a fixed authored node (or a procedurally instantiated slot with authored parameters) visible from the start; (c) generated content must tag tutorial steps as funnel events for instrumentation. The skip flag for returning players (see Anti-pattern A.1 below) must be queryable from generated content — never hardcode a forced tutorial as the only path to main content.

---

## Anti-pattern quick-reference

| Anti-pattern | Mechanism of harm | Fix |
|---|---|---|
| Unskippable tutorial wall | Removes agency at the moment player trust should be established; experienced players form negative first impressions | "Played before?" skip flag; replace forced sequence with optional, revisitable reference |
| Teaching the obvious | Wastes instruction budget on non-information; player skips tutorial, misses the one non-obvious item buried within | Assume genre competence; teach only what is unique to this game |
| Mechanic taught but never reinforced | "Burned-out" skill atom (Cook); player learns the mechanic is optional; it disappears — then reappears unexpectedly | Every tutorial mechanic must appear as required or rewarded within 30 minutes of post-tutorial play |
| Information ambush | Directly violates the 3-item working memory limit (Hodent); items compete for encoding; most fail | Deliver each system's introduction separately, in context, at the time of first need |
| Punishment during learning | Stress impairs memory formation; player learns "this game punishes mistakes," not "this mechanic works this way" (Hodent GDC 2016) | No permanent loss during the safe-space phase; quick reset at most; penalties belong in the challenge phase |

---

## Caveats

- **Expert designers cannot design for novices without playtesting.** The curse of knowledge is the primary obstacle to effective tutorial design: designers who understand a system cannot reliably predict what is confusing about it. Portal's developer commentary is a field record of playtesting-driven iteration. No amount of theoretical correctness substitutes for watching a new player attempt the tutorial. (Kim Swift, GDC 2008: "Sit down and watch people play your game. Don't just have them send you reports.")
- **Genre conventions change the baseline.** A Soulslike player entering their fifth FromSoftware game has mechanic schema a first-time player does not. Safe-space duration and permissible punishment scale with genre familiarity. This document's defaults assume no genre familiarity (the most conservative universal baseline). For genre-experienced audiences, safe-space sections can be shorter and combination beats can arrive earlier.
- **Mobile/F2P metrics may not generalize to premium/console.** D1 retention benchmarks and funnel analytics come overwhelmingly from mobile data. Premium players paid to be there and have different churn profiles. The principles (working memory limits, four-beat structure, just-in-time delivery) are universal; the specific metrics (D1 28–33%) are mobile-specific.
- **The implicit/explicit trade-off is audience-dependent.** Heliyon 2022 (PMC9676530): implicit tutorials rated 0.6/5.0 less boring; explicit rated 0.69/5.0 more helpful for medium-skill players on complex mechanics. Games with a wide expertise range benefit from a hybrid: implicit for exploration, explicit (but dismissible) for the mechanic with no genre analogue.
- **Procedurally generated tutorials have not been validated at commercial scale.** Green et al. (arXiv 1807.06734) demonstrates feasibility in Mario-framework environments. No publicly documented commercial game has shipped a fully procedurally generated tutorial for a novel mechanic set. The dependency-graph approach is theoretically sound; its production reliability at scale is unproven. Any generated tutorial must be playtested against the same standard as a hand-authored one.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
