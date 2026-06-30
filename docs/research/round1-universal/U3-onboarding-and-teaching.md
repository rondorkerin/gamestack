# U3 — Onboarding, Tutorialization, and the First-Time-User Experience

> **Skill context:** Genre- and engine-agnostic. Content may be AI-authored/procedural. Rules are stated as imperatives; every failure mode is named. Procedural/headless-generation implications are called out per sub-domain. Verified claims tagged ✅ (≥ 2 independent/primary sources), ⚠️ (single or secondary source), ❌ (refuted by evidence).

---

## 1. TL;DR

- **The best tutorial is a level, not a pop-up.** Super Mario Bros. 1-1, Portal's Test Chamber 00, and Hollow Knight's opening cavern each teach through structured play: first in a no-risk zone, then under live pressure, then in combination, then with a twist. ✅
- **Cognitive load is the primary enemy of onboarding.** Players can hold roughly 3 concurrent concepts in working memory. Exceeding that threshold during instruction causes encoding failure — the mechanic goes untaught even if it was displayed. ✅
- **Just-in-time beats front-loaded.** Players prefer instruction delivered at the moment of need; research shows front-loading causes higher drop-off and lower comprehension than context-triggered teaching. ✅
- **Mobile Day-1 retention averages ~28–33%; most loss happens during or immediately after the tutorial funnel.** Every tutorial step is a potential exit point and must be tracked as a funnel metric. ✅
- **Procedurally generated mechanics need a generated teaching ramp.** Build a mechanic dependency graph at content-generation time; introduction order must follow the topological sort of that graph or players will encounter combinations they cannot understand. ⚠️

---

## 2. Key Findings

**1. The Four-Beat Teaching Pattern is the dominant verifiable structure for mechanic introduction.** ✅  
Named by Nintendo's Koichi Hayashida as *kishōtenketsu* and independently documented across SMB 1-1, Portal, and Celeste: (1) introduce mechanic in a consequence-free space; (2) test mastery under real stakes; (3) combine with a previously learned mechanic; (4) deliver a twist that recontextualizes mastery. Sources: Hayashida via *Gamedeveloper.com* ("The Secret to Mario Level Design," 2015); *Combine OverWiki* Portal developer commentary (2007); Miyamoto interview via *MCV/DEVELOP* (2015).

**2. Show-don't-tell has a testable condition: the game must be completable by a player who reads nothing.** ✅  
Affordance-based teaching (visual cues that signal function), skill gates (barriers solvable only by the taught action), the demonstrative method (enemy eats a crow before the player encounters it), and trial-and-error with minimal punishment all enable text-free instruction. Sources: Game Developer "Methods of Creating Invisible Tutorials"; Valve developer commentary on Portal chambers; academic study (Heliyon, 2022, PMC9676530).

**3. Working memory has a firm ceiling of ~3 simultaneous novel items during learning.** ✅  
Celia Hodent (PhD Psychology, former Director of UX, Epic Games) states the working-memory constraint directly: "3 items could be, for example: 1-using an ability to 2-react to enemy attack...while 3-dodging enemy projectile." Exceeding this produces encoding failure — the mechanic is displayed but not learned. Source: Hodent, GDC 2016 "The Gamer's Brain, Part 2: UX of Onboarding and Player Engagement"; Hodent, *The Gamer's Brain* (CRC Press, 2017).

**4. Punishing players during instruction reduces retention.** ✅  
Hodent observes: "players who die or are having difficulties during the onboarding are less likely to retain." This is neurologically consistent: stress hormones during failure compete with encoding. Safe-space introduction before staked testing is not optional difficulty reduction — it is a prerequisite for learning. Source: Hodent GDC 2016; Game Developer "Teaching the Player" (Wordware Game Developers Library excerpt, flylib.com).

**5. Just-in-time tutorial delivery outperforms front-loaded instruction on player preference and retention metrics.** ✅  
Academic research (Heliyon 2022, *Learning to play: understanding in-game tutorials with a pilot study on implicit tutorials*, PMC9676530) shows players rate just-in-time access as having a "positive effect on learning of the gameplay." Industry practice aligns: F2P FTUE guides consistently recommend delivering mechanic introductions at the moment of first need, not pre-game. Sources: Heliyon 2022; GameAnalytics FTUE guide.

**6. D1 mobile game retention is ~28–33%; tutorial funnel drop-off is the dominant cause of early churn.** ✅  
Industry benchmarks (Mistplay, Segwise, Maf.ad, multiple sources 2023–2024): average D1 retention 26–33% on iOS, 25–27% on Android; D7 drops to 8–13%; D30 to ~6%. Most loss concentrates in or immediately after the tutorial. Sources: Mistplay "The big list of mobile game retention benchmarks"; Segwise; Solsten D1/D7/D30 study.

**7. Implicit tutorials are less boring and more enjoyable for experienced players; explicit tutorials are perceived as more helpful for medium-skill players learning complex mechanics.** ✅  
Heliyon 2022 pilot study (PMC9676530): implicit tutorials rated 0.6/5.0 less boring; explicit tutorials rated 0.69/5.0 more helpful for complex-mechanic comprehension. Neither type universally dominates — player expertise and mechanic complexity determine the optimal blend. Source: Heliyon 2022 PMC9676530.

**8. The Tutorial Tax (over-tutorialization) actively damages engagement and word-of-mouth.** ✅  
Forced pre-gameplay tutorials implicitly communicate the game is too complex to understand unaided, undermining player self-efficacy. Tutorials are not memorable selling points; tedious ones actively discourage recommendations. Wayline "The Tutorial Tax" (2023); TV Tropes "Forced Tutorial" (community consensus); GDC community discussion.

**9. Procedural tutorial generation is a solved research problem, but requires a mechanic dependency graph as input.** ⚠️  
Green et al. (arXiv 1807.06734, FDG 2018) demonstrated that levels teaching specific Mario mechanics can be evolved by limiting agent abilities and finding levels that fail without the target skill. The AtDelfi system (arXiv 1807.04375) builds a mechanic graph and traces critical paths. Both require the game to explicitly represent mechanic dependencies. Source: arXiv 1807.06734; arXiv 1807.04375 (single domain — Mario).

**10. The "loops and arcs" distinction predicts onboarding architecture.** ✅  
Daniel Cook (Lostgarden, 2012): loops are repeatable skill-building structures; arcs are one-way information deliveries. Front-loaded tutorials are pure arcs and suffer rapid burnout. Skill-based onboarding embeds teaching inside loops (the skill atom feedback cycle). Understanding which category each of your tutorial moments falls into determines whether it will stick. Sources: Cook, "Loops and Arcs" (Lostgarden, 2012); Cook, "The Chemistry of Game Design" (*Gamedeveloper.com*, 2007).

---

## 3. Details by Sub-Domain

---

### 3.1 The Four-Beat Teaching Pattern (Introduce → Test → Combine → Twist)

#### Background

Nintendo's Koichi Hayashida formalized the structure as *kishōtenketsu*, derived from Chinese/Japanese four-panel comic writing (ki-shō-ten-ketsu = introduction-development-twist-conclusion). Shigeru Miyamoto, who drew comics before designing games, introduced this to Nintendo's level design philosophy. It is independently documented in SMB 1-1 (1985), the Portal test chambers (2007), Celeste (2018), and Super Mario 3D World (2013).

---

**Rule 3.1.1 — Introduce each new mechanic exactly once, in a consequence-free space, before staked encounters use it.**

> The safe space must have no loss condition, or a trivially reversible one, so all player attention is on observing the mechanic rather than surviving.

*Exemplar:* Super Mario Bros. World 1-1 (Miyamoto, 1985). The first pit has a filled-in bottom — a player cannot die falling in. The immediately following pit is open and lethal. Players who explored the safe pit carry the jump-length lesson into the lethal one. Noted by multiple SMB 1-1 analyses (nocontextculture.com 2025, gamerevolution.com, CBR.com); Miyamoto confirmed via MCV/DEVELOP interview.

*Exemplar:* Hollow Knight's Crossroads opening (Team Cherry, 2017). A ground dip that cannot kill teaches jumping; a breakable wall that blocks progression teaches attacking; the first Crawlid encounter occurs in a wide corridor where flanking is impossible. Source: indiegameculture.com "Hollow Knight's Tutorial Is A Design Masterclass."

*Test for:* Can a player who has never seen a mechanic encounter the mechanic in its first appearance without taking damage or losing progress? If no, move that introduction earlier or add a buffer space.

*Failure mode:* **"Ambush introduction"** — the first time a mechanic is needed, it is also immediately lethal (e.g., a fall that requires wall-jumping to survive, before wall-jumping has been shown). Player dies, attributes it to bad luck rather than a lesson, and forms no useful mental model.

---

**Rule 3.1.2 — Follow the safe introduction with a staked test that can only be cleared by applying the just-taught mechanic.**

> The test must be solvable only one way so that success is unambiguous evidence of understanding, not luck.

*Exemplar:* Portal Test Chamber 01 (Valve, 2007). After players see themselves through the first portal (safe familiarization), the next chamber requires them to walk through a portal to progress — no other path exists. "This puzzle requires walking through a minimum of five portals in a specific order" (Robin Walker, developer commentary) to prevent accidental completion. Source: *Combine OverWiki* developer commentary.

*Exemplar:* SMB 1-1 pit sequence described above — the open pit is the staked test.

*Test for:* Can the player complete the test segment without executing the target mechanic? If yes, tighten the skill gate.

*Failure mode:* **"Bypassed gate"** — the test segment has an unintended alternate solution. In Portal, playtester bypassed a box-button puzzle by portaling boxes directly through. Solution required a glass barrier (developer commentary, Chet Faliszek).

---

**Rule 3.1.3 — Introduce a combination scenario that requires two or more previously taught mechanics to solve simultaneously.**

> Never combine mechanics the player has not each individually tested. Combination is a multiplier of complexity, not an additive.

*Exemplar:* Mega Man (Capcom, 1987). Each defeated boss grants a weapon effective against another boss. Players who have individually mastered jumping, shooting, and weapon-switching must combine all three in boss sequences. Stage enemy placement is tuned to the weapon acquired from a prior boss, making acquisition order a soft dependency. Source: daverupert.com, kokutech.com Mega Man X analysis.

*Exemplar:* Celeste Chapter 1 (Maddy Thorson / Noel Berry, 2018). After individually teaching climbing and dashing, the game presents a crumbly wall that requires dashing into it — combining the two. "Players can try running, jumping or dashing into it, and going with the latter option, the player dashes into the wall and discovers a secret pathway." Source: Medium/@josephdiamond, mimidoshima.wordpress.com.

*Test for:* List the mechanics required to solve each combination puzzle. Are all of them independently gated and passed earlier? Mark any orphaned prerequisites as a design debt.

*Failure mode:* **"Premature combination"** — player is asked to combine mechanics before any of them are individually fluent. Typical in strategy games that introduce unit A, unit B, and synergy all at once. Results in cognitive overload; player fails to encode any of the three.

---

**Rule 3.1.4 — Deliver a twist (ten) that recontextualizes the mastered mechanic in an unexpected application, then a conclusion (ketsu) that lets the player demonstrate mastery.**

> The twist is not a new mechanic; it is the same mechanic applied in a way the player did not predict. It should produce an "aha" moment, not confusion.

*Exemplar:* Koichi Hayashida on kishōtenketsu: "something crazy happens that makes you think about it in a way you weren't expecting" — then "you get to demonstrate, finally, what sort of mastery you've gained over it." Hayashida via *Gamedeveloper.com* 2015; *MCV/DEVELOP* "Nintendo's level design secrets in four steps."

*Exemplar:* Portal's Companion Cube / incinerator sequence. Players spend a chamber with the cube (build attachment); are forced to incinerate it; then use the incinerator against GLaDOS. "A nice dramatic payoff" (Jeep Barnett, developer commentary). The twist is mechanical (the incinerator as a weapon) and emotional (attachment to cube as teaching investment). Source: *Combine OverWiki* developer commentary.

*Test for:* Does the twist require understanding of the earlier safe introduction, not new information? Does the conclusion provide a visible signal of success (door opens, enemy dies, puzzle clears) that feels earned?

*Failure mode:* **"False twist"** — the twist is actually a new mechanic introduced without a safe-space phase, disguised as a surprise. Player cannot complete it without external help.

---

**Procedural implication (3.1):**  
When generating content for a procedurally defined mechanic set, extract the four-beat structure as a template: (a) generate a safe-space room using only the target mechanic with no enemies or timers; (b) generate a gated room solvable exclusively by that mechanic; (c) generate a room requiring the new mechanic plus one previously cleared prerequisite; (d) generate a room where the mechanic is applied inverted or in an unexpected context. This template is parameterizable given a mechanic definition and its prerequisites. Hand-authoring or targeted playtesting is required to verify that the safe-space room does not accidentally introduce a loss condition and that the gated room lacks bypasses — these are the hardest constraints to auto-validate.

---

### 3.2 Show-Don't-Tell: Teaching Through Level Design and Consequence

#### Background

"Show, don't tell" in games means that mechanics are communicated through environmental design, visual affordances, consequence, and player observation — not text pop-ups or forced cutscenes. The canonical case studies are SMB 1-1 (mechanic discovery through map layout), Portal (spatial understanding through self-observation), and Dark Souls (danger communication through environmental cues and NPC behavior). Academic term: *implicit tutorial* (Heliyon 2022).

---

**Rule 3.2.1 — Design every mechanic's first introduction so that a player who reads nothing can discover it through action and observation.**

> If text must be displayed to make a mechanic understandable, the level design has failed to communicate it. Text may reinforce, but must not be the primary channel.

*Exemplar:* Portal Test Chamber 00. "We deliberately positioned this first portal to ensure that players will invariably see themselves" through it (Kim Swift, developer commentary). The self-view teaches spatial looping before any button is pressed. Playtesting revealed "players will rarely look up without serious prompting," so pistons and ladders were added to force upward attention. Source: *Combine OverWiki* developer commentary.

*Exemplar:* Breath of the Wild's Great Plateau (Nintendo, 2017). The only direction is implicit: Link starts on terrain so elevated that jumping down would mean death, creating a natural fence. Every shrine and tool is in visual line-of-sight from the starting point. Hyrule Castle's storm cloud is permanently visible, establishing the long-term goal without a single cutscene. Source: eliterev.wordpress.com, uxdesign.cc (Javier Miguelez).

*Test for:* Playtest with a player who is instructed to skip all popups and press no buttons until they decide to on their own. Do they correctly discover the core action within 60 seconds of first encountering a new mechanic? If not, the environmental cue is insufficient.

*Failure mode:* **"Text dependency"** — the mechanic is physically present but not learnable without reading an adjacent popup. When players skip the popup (and research shows they will), the mechanic is invisible. Causes a sudden difficulty spike the designer did not intend.

---

**Rule 3.2.2 — Use affordances to signal mechanic function visually before the player interacts.**

> Affordances are visual cues that communicate use: spikes signal danger, coins signal collection, shiny surfaces signal interactivity, glowing objects signal importance.

*Exemplar:* SMB 1-1 Goomba approach. The Goomba moves toward Mario from the right. Players instinctively move right (the only safe direction leads there); the Goomba is unavoidable. The result is a forced first encounter where either jump or death occurs — and death is recoverable within seconds. The Goomba's eyes and movement design signal hostility without a label. Source: nocontextculture.com 40th anniversary analysis, gamerevolution.com.

*Exemplar:* Portal surface affordances. Designers created visual hotspots using "round objects and sharp objects" to guide attention toward interactive surfaces (Paul Graham, developer commentary). Orange portal color was tested: "playtesters often assumed that orange portals were exit-only" (Kerry Davis) — a false affordance — so a mandatory orange-entry chamber was inserted to break the misconception. Source: *Combine OverWiki*.

*Test for:* Remove all text from the tutorial area. Can a new player identify which surfaces are interactive, which are dangerous, and which are background? Run a 10-person first-look study and count failures.

*Failure mode:* **"False affordance"** — a design element looks interactive but is not (or vice versa). Creates a mental model mismatch that must be actively unlearned. Portal's orange portal assumption is a real-world case; the solution (forced orange entry) is a good pattern.

---

**Rule 3.2.3 — Use the demonstrative method (safe observation before interaction) for mechanics that are lethal or irreversible on first contact.**

> Show the mechanic operating on a non-player target before requiring the player to engage with it.

*Exemplar:* Half-Life 2 Barnacle (Valve, 2004). A Barnacle (ceiling trap) eats a crow before the player encounters one. The observation teaches the mechanic without player cost. Source: Game Developer "Methods of Creating Invisible Tutorials."

*Exemplar:* Dark Souls (FromSoftware, 2011). The first Black Knight is visible — and typically ignores the player — before it is encountered as a threat. The asylum demon is introduced through a cutscene of it breaking a door, showing its scale and attack style before combat begins. Source: game-wisdom.com "The Design Lessons Designers Fail to Learn From Dark Souls."

*Test for:* List every mechanic that can result in instant loss. Does each one have a demonstrative moment where the mechanic operates on the environment or an NPC before the player must engage it?

*Failure mode:* **"Blind introduction"** — the first time a mechanic is seen, the player is already inside it. Classic: timed disappearing platforms with no preview of the timing cycle. Players fall to their deaths three times before they understand the cycle is regular.

---

**Rule 3.2.4 — Teach through consequence, not through warning.**

> A player who experiences a negative consequence and understands its cause learns more durably than a player who is told a consequence will occur.

*Exemplar:* SMB 1-1 mushroom-vs-Goomba dilemma. The first mushroom bounces off a pipe and chases Mario, moving at the same speed as a Goomba and in the same direction. New players often flee the mushroom, confusing it with an enemy, until they catch it by accident or watch it bounce off the screen edge. The design teaches through the experience of confusion and resolution, not a label. Miyamoto: "When they see a coin, it'll make them happy, and they'll want to try again." Source: Miyamoto via multiple sources including nocontextculture.com, blog.adafruit.com.

*Exemplar:* Spelunky (Derek Yu, 2012). Every death has a clear single cause (spike, enemy, arrow trap). The roguelite loop — die, understand why, try again — is the tutorial. Derek Yu confirmed this as intentional: "you are learning the overall composition, understanding the overall system." Source: Gamedeveloper.com Spelunky analysis, gamedevpills.com.

*Test for:* After every failure moment in the tutorial area, can the player articulate within 5 seconds what caused the failure? If not, causality is not clear enough to teach.

*Failure mode:* **"Opaque death"** — player fails but cannot identify the cause. Common in games with overlapping hitboxes, off-screen hazards, or damage-over-time with no visual indicator. The player forms no useful mental model; they just avoid that area.

---

**Procedural implication (3.2):**  
When generating a level that introduces a procedurally defined mechanic, the generation system must produce: (a) at least one interactive object with an affordance matching the mechanic's function (e.g., a pressure plate that looks depressible, not a flat floor tile); (b) a demonstrative sequence (an NPC or object that triggers the mechanic first if the mechanic is lethal); (c) a consequence chain where failure on first attempt has a visible cause. These are constraints on the generator, not post-hoc authoring. If the mechanic description contains the fields `affordance_visual`, `demonstrable_target`, and `failure_consequence_visible`, all three can be auto-validated before the level is served to a player. Hand-authoring is required for any mechanic whose affordance cannot be expressed in the game's existing visual language.

---

### 3.3 Progressive Disclosure and Scaffolding

#### Background

Progressive disclosure in games means deferring access to mechanics, information, and complexity until the player has demonstrated readiness for them. The opposing pattern (front-loading) delivers everything upfront. Daniel Cook's skill atom framework (Lostgarden, 2007; 2012) provides the theoretical basis: skill atoms are discrete feedback loops; players can only process atoms they have existing prerequisites for; chaining atoms in dependency order is the only way to build complex competence without overload. Csikszentmihalyi's flow channel (1990; applied to games by Koster 2004) provides the motivational framework: challenge must scale with demonstrated skill or the player enters boredom (underchallenge) or anxiety (overchallenge).

---

**Rule 3.3.1 — Introduce no more than one genuinely new mechanic per level segment. Complexity must be additive, not simultaneous.**

> "One new thing per beat" is the operative rule. A new enemy type, a new platform behavior, and a new power-up are three mechanics; introducing all three in one segment will exceed working memory and teach none of them.

*Exemplar:* Celeste Chapter structure (Maddy Thorson, 2018). The game introduces one movement mechanic per chapter: dash in Chapter 1, wall-jumps in Chapter 2, dream blocks in Chapter 3. Each chapter's levels combine only the current chapter's new mechanic with all previously mastered ones. Source: Medium @josephdiamond; celeste.ink wiki.

*Exemplar:* Portal puzzle sequencing. "This puzzle introduced too many new concepts at once, which frustrated playtesters" (Chris Chin, developer commentary). Two introductory chambers were inserted before it. The development commentary is a field record of progressive disclosure applied iteratively via playtesting. Source: *Combine OverWiki*.

*Test for:* Build a mechanic introduction timeline. Count the number of new mechanics per level. Any level introducing more than one new mechanic requires justification; more than two is almost always a design error.

*Failure mode:* **"Feature dump"** — common in strategy and RPG games that unlock crafting, skill trees, faction management, and economy systems in the first 30 minutes. Players learn none of them. MMORPG tutorials are the canonical example of this failure.

---

**Rule 3.3.2 — Deliver each mechanic's tutorial at the moment of first need (just-in-time), not pre-emptively.**

> Pre-emptive tutorials are arcs (Daniel Cook): one-way information deliveries that are forgotten by the time they are needed. Just-in-time instruction is embedded in the loop where the mechanic will be used.

*Exemplar:* Breath of the Wild shrine introductions. Each shrine's opening room contains the new rune ability. The ability is unlocked and immediately usable before the player exits that room. The shrine is the tutorial; the tutorial is the game. Source: uxdesign.cc (Javier Miguelez); eliterev.wordpress.com.

*Exemplar:* Fortnite's weak-point system (Celia Hodent). The harvesting UI initially failed because players overlooked it entirely in the tutorial. Hodent's team made it a skill-tree reward rather than immediate tutorial content — players were shown the system only after reaching a milestone, making it "a meaningful reward with more fanfare." Source: Hodent, Medium/ironsource-levelup.

*Test for:* For each mechanic introduced in the tutorial, measure the time between instruction and first required use. If that gap exceeds 5 minutes of play, the player has likely forgotten the instruction. Either move the instruction closer to use, or add a just-in-time contextual reminder.

*Failure mode:* **"Frontloaded dump"** — the game presents a 10-minute unskippable tutorial before the player can touch the main game. Retention of any individual mechanic is near zero; player frustration is high. Reported consistently in forced-tutorial forum criticism (NeoGAF "Forced tutorials in Modern Gaming").

---

**Rule 3.3.3 — Build a skill dependency map and enforce its topological sort as the introduction order.**

> Mechanic B cannot be introduced before Mechanic A if B requires A to execute. Map these dependencies explicitly and treat violations as hard errors.

*Exemplar:* Daniel Cook's skill chains (Lostgarden, "The Chemistry of Game Design," 2007): "multiple atoms link together in skill chains — directed graphs showing how mastered skills enable new ones." Players cannot predict value "a couple atoms down the chain," so immediate rewards keep them progressing. The chain is the implicit curriculum.

*Exemplar:* Mega Man boss order. The intended boss order is communicated by weapon effectiveness (water weapon beats fire boss). Players who discover this order experience a clean skill chain. Players who discover it out of order experience the game as unfairly difficult. The dependency is real; its communication is what varies. Source: daverupert.com Mega Man 11 analysis.

*Test for:* Draw the dependency graph. Topologically sort it. Does the game introduce mechanics in that order? Any violation is a potential "locked out" moment.

*Failure mode:* **"Prerequisite skip"** — game introduces Mechanic C, which requires B, which requires A, but the player has only seen A. The player cannot execute C, does not understand why, and experiences it as the game being broken.

---

**Rule 3.3.4 — Show the player the lock before giving them the key. Tease future mechanics visually to create motivation for learning prerequisites.**

> Hodent: "Show the locks before giving the keys. The locks will tease and motivate the players to figure it out." Empty skill tree slots, locked doors, inaccessible areas — all motivate progression toward the mechanic that clears them.

*Exemplar:* Breath of the Wild's Great Plateau from above. From the starting point, Link can see the entirety of Hyrule — dozens of mechanics, locations, and secrets visible but inaccessible. This creates a permanent pool of curiosity-driven motivation during the tutorial. Source: gamerant.com BOTW Great Plateau analysis; uxdesign.cc.

*Exemplar:* Metroidvania locked doors. The genre's core loop — see locked door, acquire key mechanic, return — is entirely built on this principle. Hollow Knight's unreachable opening above the starting point signals return-later exploration. Source: indiegameculture.com Hollow Knight analysis.

*Test for:* Does the tutorial area contain at least one visible element the player cannot yet access? Does the mechanic that unlocks it appear within 15 minutes of play?

*Failure mode:* **"Invisible future"** — tutorial area is self-contained with no visible content beyond it. Player has no motivation to learn current mechanics except abstract progress. Common in mobile games with a gate between tutorial and main game.

---

**Procedural implication (3.3):**  
The mechanic dependency graph (Rule 3.3.3) is the generator's primary input. Given a set of mechanics M = {m₁, m₂, ... mₙ} with dependencies D (a directed acyclic graph), the tutorial generator must produce content in topological order. "Locks" can be auto-placed as unreachable geometry or locked objects in the introduction level for any mechanic that appears beyond the current introduction scope — this is a mechanical rule that requires only knowledge of the mechanic list, not hand-authoring. Verifying that no level contains a mechanic prerequisite gap requires running the A*-with-limited-ability test from Green et al. (arXiv 1807.06734): ablate the target mechanic from the agent's capability set and confirm the level fails.

---

### 3.4 FTUE and Early Retention: The First Session, the First Hour

#### Background

FTUE (First-Time User Experience), "first hour," and "learning experience" are industry synonyms for the design zone covering a new player's initial session. In mobile/F2P, this zone is a conversion funnel with measurable step-by-step drop-off. In premium games, it determines whether a player returns for session 2. The first-hour experience is the highest-ROI design surface in any game because it is the universal gateway for all players.

---

**Rule 3.4.1 — The player must experience a moment of genuine fun within the first 2–3 minutes. Do not spend this time on narrative setup, account creation, or system tutorials.**

> The core value proposition of the game must be playable immediately. Everything else is friction. Players who do not understand the game's fun within 2–3 minutes are statistically very likely to churn.

*Exemplar:* Fortnite (Epic, 2017). The glider mechanic is the first action a player takes in every match. No account wall (unless required by platform), no mandatory credits sequence — you fall from the sky with 100 players visible on screen. The core chaos is the first moment. Source: Hodent, Medium/ironsource-levelup; celiahodent.com.

*Exemplar:* Candy Crush Saga (King, 2012). First playable move is within 5 seconds of game launch on mobile. No text walls. The match-3 mechanic is self-evident from the clustered candy layout. Source: GameAnalytics FTUE guide (implicit from best-practice framing).

*Test for:* Time-stamp the first moment of genuine player-controlled, in-genre play. Is it under 180 seconds from app launch? Every 30 seconds beyond that costs measurable D1 retention.

*Failure mode:* **"Pre-fun gauntlet"** — account creation, unskippable cutscenes, terms of service, and forced tutorial precede any gameplay. Players who abandon at this stage are invisible in most analytics (they leave before event tracking fires). Common in older MMORPGs and some premium console titles.

---

**Rule 3.4.2 — Instrument the tutorial as a funnel and find the drop step. Fix it before launch.**

> Every tutorial step where a player can exit is a funnel step. Identify the single highest drop-off point. That step has a design flaw. Fix it, re-test, repeat.

*Exemplar:* GameAnalytics FTUE guide: "Create a tutorial funnel by creating events for different steps in the tutorial, and by observing the funnel, you will be able to detect the main flaws." This is industry standard for any F2P title.

*Data:* D1 retention averages 28–33% (Mistplay, Segwise 2023–2024). A seamless, engaging FTUE can increase retention rates by up to 50% (Segwise). Every percentage point of D1 improvement compounding to D7 and D30 has large LTV implications.

*Test for:* Pre-launch: run a 20-person playtest where every action is recorded. Build the funnel manually. Identify the single step with the largest drop and fix it. Post-launch: instrument with analytics on day one.

*Failure mode:* **"Uninstrumented tutorial"** — game launches without event tracking on tutorial steps. Designer cannot identify where players leave. Impossible to iterate without this data. Discovered after release when D1 is already measured as too low.

---

**Rule 3.4.3 — Ensure the player experiences a small, clear win before encountering the first obstacle.**

> Psychological need for competence (Cook, Koster, Hodent all converge here) means that players who feel capable continue playing. An early win creates the emotional safety net for the first failure.

*Exemplar:* GameAnalytics FTUE guide: "Award premium currency for tutorial completion; ensure victories occur during tutorials." The win teaches monetization behavior and builds confidence simultaneously.

*Exemplar:* SMB 1-1 coin boxes. The first coin block is unavoidable and immediately rewarding. Miyamoto: "When they see a coin, it'll make them happy, and they'll want to try again." The coin is a win token, not just a score unit. Source: blog.adafruit.com Miyamoto; nocontextculture.com.

*Test for:* In the first 5 minutes, does the player succeed at something with clear visual/audio feedback before encountering their first failure? Measure with playtest; note facial response at first success vs. first failure.

*Failure mode:* **"Cold open failure"** — game begins with a difficulty spike or a death-eligible encounter before any success moment. Players attribute failure to the game being unfair rather than to a lesson to learn.

---

**Rule 3.4.4 — Personalization introduced in the FTUE substantially increases engagement. Offer it early.**

> "Post-customization retention and engagement often spike" (GameAnalytics). Avatar creation, base naming, and appearance choices create emotional investment before the player has any in-game progress to attach to.

*Exemplar:* Nearly every RPG opens with character creation. While this is also genre expectation, its retention effect is demonstrable: players who have named and designed a character are harder to lose than those who have not. Source: GameAnalytics FTUE guide.

*Test for:* Does the tutorial include at least one personalization moment (naming, appearance, choice of starting ability, etc.) before the first loss condition?

*Failure mode:* **"Generic start"** — player controls a nameless default character with no emotional investment. Tutorial becomes an obligation rather than an introduction to *their* character.

---

**Rule 3.4.5 — Establish a long-term goal within the first session, visible but not immediately reachable.**

> Without a long-term anchor, players who run out of short-term motivation have no reason to return. The goal must be visible enough to create anticipation.

*Exemplar:* Breath of the Wild — Hyrule Castle is visible from the Great Plateau's summit. It is the entire game's goal, shown in the first minute, completely inaccessible. Source: gamerant.com; eliterev.wordpress.com.

*Exemplar:* Hades (Supergiant, 2020) establishes Zagreus's escape goal in the first run — and makes the player fail it. The visible goal (exit) combined with narrative motivation (finding mother) creates a return loop. Source: christi-kerr.com Hades dialogue analysis.

*Test for:* Can a player who has played 20 minutes articulate a single long-term goal that is not achievable in this session? If not, the game has retention risk at the session boundary.

*Failure mode:* **"Goalless tutorial"** — the tutorial teaches mechanics but does not establish narrative or strategic direction. Player completes tutorial with no sense of what happens next. Common in mobile games whose core loop hasn't been connected to a meta-layer.

---

**Procedural implication (3.4):**  
The first-session arc must be authored or at minimum scaffolded at a meta-level, even in procedurally generated games: (a) the generator must ensure the player's first generated room/level is winnable and contains an early-win token; (b) the long-term goal must be a fixed authored node (or a procedurally instantiated slot with authored parameters) visible from the start; (c) personalization can be procedurally offered (name entry, palette selection) but requires a pause in the generation flow. Funnel instrumentation is always developer-side, not generator-side, but generated content must tag tutorial events for the instrumentation to read.

---

### 3.5 Anti-Patterns

The following patterns are reliably harmful across genres. Each has multiple documented instances of player churn, negative reviews, or explicit designer regret.

---

**Anti-pattern 3.5.1 — The Unskippable Tutorial Wall**

> A mandatory pre-game sequence that teaches basics before the player can make any choice, can be 5–30 minutes long, and cannot be bypassed by experienced players.

*Mechanism of harm:* Experienced players (most players after a game's first week) are forced through content irrelevant to them. Player agency is removed at the moment player trust should be established. Experienced players form negative first impressions; their reviews damage discovery. Source: NeoGAF "Forced tutorials in Modern Gaming"; TV Tropes "Forced Tutorial."

*Named instances:* Kingdom Hearts (multiple games in the franchise), Metal Gear Solid V: The Phantom Pain (20-minute hospital sequence), early Dragon Age: Origins.

*Fix:* Ask "have you played before?" on first launch and skip to main game. Or: replace the forced sequence with an optional, revisitable reference and begin the game immediately.

*Procedural implication:* Generated tutorials must have a skip flag that is queryable by the runtime. Never hardcode a forced tutorial as the only path to the main generated content.

---

**Anti-pattern 3.5.2 — Teaching the Obvious**

> Tutorial explicitly describes actions that any player capable of running the game already knows: "Press the joystick to move." "Use WASD to walk." "Click to select."

*Mechanism of harm:* Communicates distrust of the player. Experienced players feel insulted. Even new players of this specific game are not new to holding a controller. Wastes the instruction budget on non-information. Source: Wayline "The Tutorial Tax"; Game Developer "Tutorialization" (archived).

*Fix:* Assume genre competence, not game competence. Teach what is unique to this game, not what is universal to the input device.

*Test for:* Read every tutorial instruction aloud. Would a player who has played any game in the same genre already know this? If yes, remove it.

*Failure mode:* **"Insulting competence"** — player skips tutorial after seeing obvious instructions, misses the one non-obvious instruction buried among them.

---

**Anti-pattern 3.5.3 — Mechanics Taught But Never Reinforced**

> A mechanic is introduced in the tutorial and then never required or rewarded for the rest of the game.

*Mechanism of harm:* Produces "burned out" skill atoms (Cook's taxonomy). Players who invest cognitive effort in learning a mechanic that disappears feel deceived. The tutorial implied the mechanic was important; the game contradicted that signal. Also inflates tutorial length without adding durable value. Source: Cook, "The Chemistry of Game Design" (skill atom burnout state); Teaching Game Mechanics hierarchy.

*Fix:* Every mechanic introduced in the tutorial must appear at least once as a required or heavily rewarded action in the first 30 minutes of post-tutorial play.

*Test for:* Map every tutorial mechanic to a post-tutorial reinforcement moment. Any mechanic with no reinforcement within 30 minutes is a candidate for removal from the tutorial.

*Failure mode:* **"Tutorial orphan"** — players reach late game without the introduced mechanic because they learned early that it was optional. Required mechanic reappears unexpectedly; player cannot execute it.

---

**Anti-pattern 3.5.4 — The Information Ambush**

> Multiple mechanics, UI elements, and narrative hooks are introduced simultaneously in a single scene or screen before the player has context for any of them.

*Mechanism of harm:* Directly violates the 3-item working memory limit (Hodent). All items compete for the same encoding resource; most fail. The screen is comprehensible in principle but incomprehensible in practice because players cannot allocate attention across the overloaded display. Source: Hodent GDC 2016; Hodent *The Gamer's Brain* (2017).

*Examples:* Many MMORPGs (World of Warcraft's first-character creation sequence circa 2004); Paradox grand strategy games (Crusader Kings II intro); complex mobile battle royale games pre-tutorial-update.

*Fix:* Deliver each system's introduction separately, in context, at the time it is first needed. Delay secondary systems until after core loop comprehension is established.

*Procedural implication:* Generators that simultaneously introduce mechanics, enemies, environment hazards, and narrative beats in the same room will reliably produce information ambushes. Separate introduction rooms per mechanic, or interleave in temporal sequence, not spatial overlap.

---

**Anti-pattern 3.5.5 — Punishment During Learning**

> The tutorial (or early introduction area) depletes lives, penalizes currency, or applies permanent consequences for failures that occur while the player is in the instruction phase.

*Mechanism of harm:* Stress during encoding impairs memory formation. Players in anxiety cannot learn efficiently (Csikszentmihalyi flow model; Hodent GDC 2016: "players who die or are having difficulties during the onboarding are less likely to retain"). The game teaches the wrong lesson: "this game punishes mistakes," rather than "this mechanic works this way."

*Fix:* No permanent loss during the safe-space phase. Quick, low-cost respawns (at most, a 3-second reset to the start of the introduction room) are acceptable. Lives, currency, and save-state penalties belong in the challenge phase, not the instruction phase.

*Exception:* Games where death-as-lesson is the core loop (Spelunky, Dark Souls, Roguelikes). In these genres, quick respawn and clear causality are the compensating mechanisms. Punishment must be swift and understood, not permanent and opaque.

*Test for:* List every consequence of failure during the first mechanic introduction. Do any of those consequences persist beyond the current attempt? If yes, move them after confirmed mastery.

---

## 4. Recommendations: Staged Steps

### Stage 1 — Pre-production: Build the mechanic dependency graph

1. List every mechanic the player will need to understand to reach the first major milestone.
2. Draw directed edges: if Mechanic B requires A to execute, draw A → B.
3. Topologically sort the graph. This is the mandatory introduction order. Treat violations as hard errors.
4. Mark every mechanic leaf node (no prerequisites) as a "Day 1 safe introduction" candidate.
5. **Threshold that changes the plan:** If the graph has more than 8 nodes before the first milestone, the milestone is too distant. Split it or simplify the mechanic set. A player cannot learn 8 new things before they care about the world they are learning them in.

### Stage 2 — Level design: Author the four-beat structure per mechanic

For each mechanic in introduction order:
1. **Safe room:** Consequence-free, affordance-visible, no enemies, no timer.
2. **Gated room:** Solvable only by the target mechanic; no alternate path.
3. **Combination room:** Requires the new mechanic plus the most recently mastered prerequisite.
4. **Twist room:** Novel recontextualization of the mechanic; player applies mastery in unexpected direction.

**Threshold that changes the plan:** If a four-beat pass takes more than 8 minutes per mechanic and you have more than 3 mechanics to introduce, the first session will be too long. Collapse adjacent mechanics that share a combination (A+B together rather than A then B) or defer one mechanic to session 2.

### Stage 3 — FTUE design: The first 5 minutes

1. Ensure fun is playable within 2 minutes of launch.
2. Insert a small, clear win with explicit feedback within 3 minutes.
3. Establish one visible long-term goal within 5 minutes.
4. Offer one personalization moment before the first loss condition.
5. Instrument all tutorial steps as funnel events.

**Threshold that changes the plan:** If the genre requires significant setup before fun is accessible (strategy, simulation, RPG), consider a "skirmish" mode or a pre-authored scenario that puts the player into an advanced state immediately, then returns to the tutorial after the hook is set.

### Stage 4 — Playtesting: Validate instruction, not difficulty

Run two separate playtest protocols:
1. **Comprehension test:** Players narrate their understanding aloud. After each tutorial section, ask what they just learned. If they cannot articulate the taught mechanic, the teaching failed — not the player.
2. **Skip-everything test:** Players are told to skip all text, dismiss all popups, and start playing immediately. Measure what they discover independently. Any mechanic not discoverable through this protocol is not implicitly taught.

Kim Swift (Portal, Valve GDC 2008): "Sit down and watch people play your game. Don't just have them send you reports... You can find out what your players actually want by watching them."

**Threshold that changes the plan:** If more than 30% of playtesters fail to articulate a core mechanic after its introduction, that mechanic's teaching beat is broken. Redesign before launch; do not rely on day-one patch.

### Stage 5 — Post-launch: Funnel analytics and iteration

1. Monitor D1, D7, D30 retention against genre benchmarks (D1: ~28–33% mobile; D7: ~8–13%).
2. Identify the single tutorial step with highest drop-off.
3. Run an A/B test on one change to that step. Measure D1 impact.
4. Iterate monthly until D1 exceeds benchmark for the genre.

---

## 5. Caveats

**Expert players cannot design for novices without playtesting.** The curse of knowledge is the primary obstacle to effective tutorial design: designers who understand a system cannot reliably predict what is confusing about it. The four-beat pattern and the implicit tutorial techniques in this document are systematically derived from playtesting data (Portal's developer commentary is a field record of playtesting-driven iteration). No amount of theoretical correctness substitutes for watching a new player attempt the tutorial.

**Genre conventions change the baseline.** A Soulslike player entering their fifth FromSoftware game has mechanic schema that a first-time player does not. Tutorial length, safe-space duration, and permissible punishment all scale with genre familiarity. This document's defaults assume no genre familiarity (the most conservative and universally applicable baseline). For games targeting genre-experienced players, safe-space sections can be shorter and combination beats can arrive earlier.

**Mobile/F2P data may not generalize to premium/console.** D1 retention benchmarks and funnel-step analytics come overwhelmingly from mobile data. Premium console games have different acquisition costs (players paid to be there), different session lengths, and different churn profiles. The principles (working memory limits, four-beat structure, just-in-time delivery) are universal; the specific metrics (D1 28–33%) are mobile-specific.

**Procedurally generated tutorials have not been validated at commercial scale.** The arXiv research (1807.06734, 1807.04375) demonstrates feasibility in Mario-framework environments. No publicly documented commercial game has shipped a fully procedurally generated tutorial for a novel mechanic set. The dependency graph approach is theoretically sound; its production reliability at scale is unproven. Any generated tutorial must be playtested against the same standard as a hand-authored one.

**The implicit/explicit trade-off is audience-dependent.** Heliyon 2022 confirms implicit tutorials benefit experienced players but may under-serve medium-skill players on complex mechanics. Games with a wide player expertise range (most mass-market titles) benefit from hybrid approaches: implicit for exploration, explicit (but dismissible) for the mechanic with no genre analogue.

---

## Primary Sources and Citations

- Miyamoto, S. via MCV/DEVELOP: "Miyamoto shares his level design secrets." https://mcvuk.com/development-news/video-miyamoto-shares-his-level-design-secrets/
- Hayashida, K. via Gamedeveloper.com: "The Secret to Mario Level Design." https://www.gamedeveloper.com/design/the-secret-to-i-mario-i-level-design
- Valve developer commentary (2007). *Portal*. Combine OverWiki: https://combineoverwiki.net/wiki/Developer_commentary/Portal
- Kim Swift / Erik Wolpaw, GDC 2008. "The Secrets of Portal's Huge Success." https://www.gamedeveloper.com/game-platforms/best-of-gdc-the-secrets-of-i-portal-i-s-huge-success
- Hodent, C. GDC 2016. "The Gamer's Brain, Part 2: UX of Onboarding and Player Engagement." GDC Vault: https://www.gdcvault.com/play/1023231/The-Gamer-s-Brain-Part ; celiahodent.com: https://celiahodent.com/gamers-brain-ux-onboarding/
- Hodent, C. (2017). *The Gamer's Brain: How Neuroscience and UX Can Impact Video Game Design*. CRC Press. https://www.amazon.com/Gamers-Brain-Neuroscience-Impact-Design/dp/1498775500
- Hodent, C. (2021). "Understanding the success of Fortnite: A UX and psychology perspective, part 2." https://medium.com/ironsource-levelup/understanding-the-success-of-fortnite-a-ux-and-psychology-perspective-part-2-eea824ae63ca
- Cook, D. (2007). "The Chemistry of Game Design." *Gamedeveloper.com*. https://www.gamedeveloper.com/design/the-chemistry-of-game-design
- Cook, D. (2012). "Loops and Arcs." *Lostgarden*. https://lostgarden.com/2012/04/30/loops-and-arcs/comment-page-1/
- Green, M.C. et al. (2018). "Generating Levels That Teach Mechanics." *FDG PCG Workshop*. arXiv:1807.06734. https://arxiv.org/abs/1807.06734
- Sarkar, A. et al. (2018). "AtDelfi: Automatically Designing Legible, Full Instructions For Games." arXiv:1807.04375. https://arxiv.org/pdf/1807.04375
- Heliyon (2022). "Learning to play: understanding in-game tutorials with a pilot study on implicit tutorials." PMC9676530. https://pmc.ncbi.nlm.nih.gov/articles/PMC9676530/
- Koster, R. (2004). *A Theory of Fun for Game Design*. Referenced via https://www.theoryoffun.com/
- Gamedeveloper.com: "Teaching Game Mechanics: A Hierarchy of Learning." https://www.gamedeveloper.com/design/teaching-game-mechanics-a-hierarchy-of-learning
- Gamedeveloper.com: "Methods of Creating Invisible Tutorials." https://www.gamedeveloper.com/design/methods-of-creating-invisible-tutorials
- Gamedeveloper.com: "How Onboarding Should be Applied to Tutorials." https://www.gamedeveloper.com/design/how-onboarding-should-be-applied-to-tutorials
- GameAnalytics: "10 Tips For A Great First Time User Experience (FTUE) In F2P Games." https://www.gameanalytics.com/blog/tips-for-a-great-first-time-user-experience-ftue-in-f2p-games
- Mistplay: "The big list of mobile game retention benchmarks." https://business.mistplay.com/resources/mobile-game-retention-benchmarks
- Segwise: "Mobile Gaming App User Retention Strategies and Benchmarks." https://segwise.ai/blog/mobile-gaming-app-user-retention-strategies
- IndieGameCulture: "Hollow Knight's Tutorial Is A Design Masterclass." https://indiegameculture.com/hollow-knight/hollow-knights-tutorial-is-a-design-masterclass/
- Wayline: "The Tutorial Tax: How Over-Tutorialization is Killing Game Discovery." https://www.wayline.io/blog/tutorial-tax-killing-game-discovery
- Daverupert.com: "How Mega Man 11's Levels Do More With Less." https://daverupert.com/2018/10/how-mega-man-11-s-levels-do-more-with-less-game-maker-s-toolkit/
- Nocontextculture.com: "How Super Mario Bros World 1-1 Revolutionized Game Design Forever." https://nocontextculture.com/2025/09/23/super-mario-bros-world-1-1-level-design-40th-anniversary/
- Eliterev.wordpress.com: "Breath of the Wild's Quiet Guidance and the Lessons of the Great Plateau." https://eliterev.wordpress.com/2017/03/16/breath-of-the-wilds-quiet-guidance-and-the-lessons-of-the-great-plateau/
- Uxdesign.cc (Javier Miguelez): "User onboarding lessons from The Legend of Zelda: Breath of the Wild." https://uxdesign.cc/user-onboarding-lessons-from-the-legend-of-zelda-breath-of-the-wild-8d22abec8342
- Game Wisdom: "The Design Lessons Designers Fail to Learn From Dark Souls." https://game-wisdom.com/critical/designers-learn-dark-souls
- XJTLU: "Video game tutorials: making the implicit explicit." https://www.xjtlu.edu.cn/en/news/2022/01/video-game-tutorials-making-the-implicit-explicit
