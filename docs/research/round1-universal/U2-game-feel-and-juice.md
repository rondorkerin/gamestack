# U2 — Game Feel & Juice: Universal Discipline Research

> **Research document** — seed for the `game-feel-and-juice` universal-craft skill.
> Written by the gamestack research agent; adversarially verified against independent sources.
> Verification tags: ✅ ≥2 independent/primary sources · ⚠️ single source · ❌ refuted or substantially contested.

---

## 1. TL;DR

- **Game feel is not decoration.** Steve Swink defines it precisely: real-time control of virtual objects in a simulated space, with interactions emphasized by polish. All three components are required; polish alone is cosmetic, not feel. ✅
- **The juice-impact curve is an inverted U.** Dominic Kao's N=3,018 controlled study (the largest to date) found that *Extreme* juiciness measurably hurts play time, player experience, intrinsic motivation, and performance — just as *None* does. The optimum is a Medium–High band. ✅
- **Latency thresholds are genre-dependent and lower than designers assume.** The commonly cited 100 ms correction-cycle ceiling applies to many games, but expert FPS players perceive latency at 15–50 ms; platform games degrade after ~50 ms. Targeting 60 Hz with 50 ms total latency is the practical floor for action games. ✅
- **Input forgiveness (buffering, coyote time, corner correction) makes games feel more responsive, not easier.** These techniques work by shrinking the gap between player intent and game response. Celeste uses at least seven independent forgiveness systems yet remains one of the hardest precision platformers ever shipped. ✅
- **For AI-generated content, a per-action "feedback budget" table is the practical implementation.** Hard-authored min/max guardrails keyed to (event type × magnitude) let a generator compose feedback consistently without inventing new intensities and without exceeding the Kao ceiling.

---

## 2. Key Findings

**1. Swink's three building blocks are mutually necessary, not additive options.** ✅  
Swink (*Game Feel*, 2009) defines the three as: (a) **real-time control** — the game responds within a player's correction cycle of under 100 ms; (b) **simulated space** — a 2D/3D world with movement, collision, and physical properties (speed, gravity, weight); (c) **polish** — audiovisual effects that emphasize interactions without changing the underlying simulation. A game that has polish but lacks tight latency still fails on feel. Liz England's review (lizengland.com) and multiple book-club discussions confirm the three are co-equal conditions, not a hierarchy.

**2. The "juice" concept as commonly taught conflates Swink's three pillars.** ⚠️  
Celia Wagar (critpoints.net, 2020) argues that influential presentations like Nijman's "The Art of Screenshake" demonstrate changes — enemy HP, fire rate, bullet size, enemy count — that are *game design* choices, not *game feel* in Swink's sense. The critique is well-founded: game feel strictly refers to the sensation of real-time control in a simulated space. Juice (audiovisual amplification) is the polish component. Both matter; conflating them causes designers to layer VFX without fixing the underlying latency or simulation fidelity. This document uses "game feel" in Swink's broad sense and "juice" as the polish sub-discipline.

**3. Hit-stop / freeze frames are the single highest-impact individual juice primitive in action games.** ✅  
A 2022 IEEE study (Lin et al., "What Features Influence Impact Feel?", GEM 2022) ranked three features as especially influential: hit stop, sound coherence, and camera control. Masahiro Sakurai independently confirms (Famitsu column #490–1, 2015, translated by Source Gaming) that hitstop's duration scales with attack damage and is individually calibrated per move. Street Fighter II uses approximately 10 frames (~167 ms) of hitstop on normal hits; Smash Bros. gives weaker projectiles minimal freeze while charging moves get extended freeze for match-defining weight.

**4. The inverted-U relationship between juice density and player outcomes is empirically verified at scale.** ✅  
Kao et al. ("The Effects of Juiciness in an Action RPG," *Entertainment Computing* 34, 2020, N=3,018) tested four conditions (None / Medium / High / Extreme) in a controlled action RPG. **Medium and High outperformed both None and Extreme on every measure**: play time, player experience (PX scale), intrinsic motivation, and raw player performance. Kao et al. (CHI 2024, pre-registered, N=1,699) further found that juice motivates primarily through curiosity and competence perception — and that amplification backfires when it impairs the player's sense of agency. Taken together: juice must communicate what happened, not merely overwhelm the senses.

**5. Input forgiveness shrinks intent-to-response lag without altering the underlying difficulty.** ✅  
Celeste's lead designer Maddy Thorson documented at least seven simultaneous forgiveness systems (Medium, 2020): coyote time (5-frame ledge leniency), jump buffering (prestored input fires on landing), corner correction for jumps and dashes, half-gravity at jump apex, wall-jump proximity window (2 px standard, ~5 px for super wall-jump), lift momentum storage, and a stamina refund grace window. The game's difficulty remained extremely high while feel remained fluid — confirming that forgiveness is latency compression, not difficulty reduction.

**6. Latency perception thresholds are lower than the 100 ms Swink baseline and vary by genre.** ✅  
Raaen's survey ("Latency Thresholds for Usability in Games," NIKT 2014) and supporting research: FPS players detect input lag at 15 ms (expert), with annoyance at 100 ms; platformers show degraded performance after ~50 ms; RTS games tolerate ~1000 ms for most actions. The practical modern target for action games running at 60 Hz is 50 ms total controller-to-screen latency. Emulation feel-degradation (often described as "wrong even when accurate") maps to sub-50 ms latency mismatches imperceptible in A/B but cumulative in play.

**7. The 12 Disney animation principles apply directly to games with one critical inversion.** ✅  
Frank Thomas & Ollie Johnston's *The Illusion of Life* (1981) codified 12 principles. Applied to games (per multiple game-animation educators including Chris Totten's "12 Principles for Game Animation," Medium; Cooper's *Game Anim*): **anticipation is desirable on enemies** (it warns players of incoming attacks) and **undesirable on player avatars** (it reads as input lag). The asymmetry is a game-specific departure from film animation, where anticipation serves only dramatic legibility.

**8. Camera feel is a first-order feedback primitive, not camera mechanics.** ✅  
Nijman's "Art of Screenshake" (INDIGO 2013, ~45 min) demonstrated camera kick as one of the most immediately impactful single changes to a flat shooter. The Borderline Games trauma-based screenshake model (blog.borderline.games) — where `trauma` accumulates from hits and decays smoothly, driving both translation and rotation — prevents the clipping-through-walls problem of pure translation shake. Lin et al.'s 2022 study independently ranked camera control among the top three impact feedback features.

**9. Pichlmair & Johansen's survey maps the field into three design domains.** ⚠️  
Their 2020 survey (200+ sources, arxiv 2011.09201) identifies three intended player experience domains: **physicality** (tuning → cohesion, predictability), **amplification** (juicing → empowerment, clarity), and **support** (streamlining → acting on player intent). This maps cleanly to Swink's building blocks: physicality ≈ simulated space, amplification ≈ polish/juice, support ≈ real-time control. Marked ⚠️ because the full paper text was inaccessible during fetch; the summary is sourced from search snippets and HuggingFace paper page.

---

## 3. Details by Sub-domain

---

### Sub-domain A — Swink's Three Building Blocks and the Definition of Game Feel

**Foundation text:** Steve Swink, *Game Feel: A Game Designer's Guide to Virtual Sensation* (Morgan Kaufmann, 2009).

#### A.1 Real-Time Control

**Rule:** Ensure the correction cycle — player reads feedback → decides → acts → game delivers new feedback — completes in under 100 ms. Any longer and the player ceases to experience seamless interaction and instead perceives individual discrete actions.

- **Exemplar:** Super Mario 64. Swink spends an extended case study on Mario 64's control-to-avatar mapping: steering, acceleration, friction, and airborne correction all obey the 100 ms constraint in every state. Players report that "Mario always does what I told him to do" — the sensation of virtuosity emerges from the loop, not from explicit teaching.
- **Test for:** total controller input → rendered pixel change latency ≤ 100 ms on target hardware at target framerate. For action genres, ≤ 50 ms at 60 fps (Raaen survey). Measure with a high-speed camera or dedicated latency tool on retail hardware.
- **Failure mode — Floaty/unresponsive controls:** latency above the perceptual threshold breaks the correction loop; the player no longer reads cause and effect as unified and loses the sense of inhabiting the avatar. Genre-specific: FPS players begin to notice at 15 ms, platform players at ~50 ms.

**Genre variation:** RTS and strategy games tolerate latency up to ~1,000 ms because the action quantum is selecting a unit or issuing a command, not steering a body through space. Match the latency budget to the action quantum of the genre.

**Procedural implication:** Procedurally generated content cannot increase latency. If a generator spawns dense particle systems or pathfinding load spikes that push frame time above 16.7 ms (60 fps), it has broken the feel contract regardless of visual quality. Profile generated scenes under peak load and reject content that drops below the frame-rate floor.

---

#### A.2 Simulated Space

**Rule:** Give the avatar a physical presence in the world — mass, speed, gravity, inertia, friction, and collision — so the player can model and predict its behavior. The simulation must be consistent and internally coherent.

- **Exemplar:** Hollow Knight (Team Cherry, 2017). Knight has distinct values for falling speed, jump height, air-turning radius, and attack knockback, all consistent across the entire game. Players learn the physics in the first five minutes and that model remains valid through the final boss. Contrast with games that change avatar physics per zone (often done to create "weighty" boss arenas) — the inconsistency breaks the learned model.
- **Test for:** the avatar's physics parameters are version-controlled constants, not per-scene variables. Any intentional physics alteration (e.g., low-gravity zone) is documented as a deliberate design choice and communicates its difference to the player through clear spatial cues before the player commits to a movement decision.
- **Failure mode — Inconsistent physics / zone-to-zone float:** a player who has internalized jump arcs finds them invalid in a new area, resulting in missed platforms that feel unfair rather than challenging.

**Genre variation:** A puzzle game (e.g., Portal) or a card game has no avatar physics at all, yet still needs consistent *system* behavior — the logic of the simulation. The building block applies: what matters is that the rules of the simulated space are predictable to the player. In a match-3 puzzle, the "physics" are the matching rules, cascade logic, and gravity of falling tiles.

**Procedural implication:** Procedural level generation must preserve the avatar's physics contract. A generator may vary tile layouts, enemy placements, and biome aesthetics, but it must not produce ceiling heights that clip the peak of the avatar's jump arc, or floor-gap widths that fall slightly outside the safe jump range without explicit difficulty-tier justification. Test generated levels with a physics-constant validator before shipping.

---

#### A.3 Polish

**Rule:** Layer audiovisual effects that emphasize and communicate the interactions in the simulation. Polish does not change what happens; it makes what happens perceptible, readable, and satisfying. Defer all polish implementation until the simulation's feel is already correct — polishing bad-feeling movement is a waste.

- **Exemplar:** Celeste (Maddy Thorson / Noel Berry, 2018). All character animations, dust particles, and trail effects are layered atop a physics simulation that already feels correct. The jump animation's hair and cape follow-through, the landing dust cloud, and the screen flash on dash all *confirm* physics events already happening — they add no new information to the game logic, only clarity and delight.
- **Test for:** remove all polish (mute sound, disable particles, strip screen effects) and test whether the core physics still feels acceptable. If the game is unplayable without the effects, the simulation is doing too little and the polish is compensating — a brittle foundation.
- **Failure mode — Polish masking bad fundamentals:** particle storms and screen shake hiding input lag or inconsistent physics. Strip effects during development; fix feel before adding juice.

---

### Sub-domain B — Input & Response

Covers: latency, input buffering, coyote time, forgiveness windows, and control-to-avatar mapping.

#### B.1 Input Latency

**Rule:** Measure and minimize total input-to-pixel latency. Report it in ms, not "frames," to account for frame-rate variation. Distinguish controller latency, engine processing latency, and display latency separately.

- **Exemplar:** Competitive FPS titles (CS:GO, Valorant). Both run at 128-tick servers (7.8 ms server update) and are optimized for sub-50 ms total controller-to-display latency. Esports peripherals (high-polling mice, 240 Hz monitors) further reduce the floor. The design is not about optics — it is about keeping the correction cycle loop intact.
- **Thresholds (✅ Raaen 2014 survey + multiple sources):**
  - Expert FPS players detect lag at ~15 ms
  - Platformers show performance degradation at ~50 ms
  - Most action genres: 100 ms is the perceivable/annoying threshold
  - RTS: tolerates ~500–1,000 ms for most interactions
  - UI menus: 100–200 ms transitions are optimal; >400 ms feels sluggish
- **Test for:** use a high-speed camera (240 fps+) or DF-Digital Foundry-style input lag tester to measure tap-to-first-pixel-change on final hardware with final build. Instrument separately: poll rate, engine frame time, display latency.
- **Failure mode — Immeasured latency:** platform teams who never instrument this find their action game feels "floaty" or "disconnected" with no diagnostic information. The most common source is uncapped rendering without vsync producing frame time spikes, not the game logic itself.

---

#### B.2 Input Buffering

**Rule:** Store player inputs for a brief window (typically 100–200 ms / 6–12 frames at 60 fps) so that an input pressed slightly too early — while the avatar is still in a previous action's recovery — executes on the first valid frame rather than being dropped.

- **Exemplar:** Street Fighter series. The fighting game input buffer allows a special move motion (e.g., quarter-circle + punch) to be recognized over a rolling window. Hitstop *extends* this window (Celia Wagar, critpoints.net): during the freeze frames of a hit, the cancel window is preserved, enabling consistent combo inputs under mechanical pressure.
- **Secondary exemplar:** Celeste (Thorson, 2020). Jump buffering stores the jump input for several frames before landing. The player who taps jump a moment before reaching the ground executes a jump on the exact first frame of landing — the input intent is honored even when the timing is imperfect.
- **Test for:** confirm that the earliest acceptable input for each action type is buffered for at least 100 ms (or a documented minimum), and that buffered inputs do not queue actions past their intent (i.e., do not cause a player to accidentally double-jump when intending a single).
- **Failure mode — Input dropped:** pressing jump slightly early produces nothing; the player is penalized for reacting at human speed. Feels like unresponsive controls even when the game is technically running at 60 fps.
- **Failure mode — Over-buffered stale inputs:** a 500 ms buffer causes the player's old input to fire long after their intent changed, creating "ghost actions" that feel like a loss of control. The optimal window is long enough to absorb human timing noise (~100 ms) but short enough not to fire stale intent (~200 ms ceiling for most actions).

---

#### B.3 Coyote Time (Ledge Leniency)

**Rule:** Allow players to jump for a brief window (typically 4–6 frames / ~67–100 ms at 60 fps) after walking off a ledge, because the human perception-reaction loop takes longer than one rendered frame.

- **Exemplar:** Celeste (Thorson, 2020) — coyote time of 5 frames (83 ms at 60 fps). The player who walks off a ledge and presses jump within 5 frames successfully executes the jump as if still on the platform. The mechanic is invisible — players perceive the game as accurate, not forgiving.
- **Genre extension:** The principle generalizes beyond platformers. In fighting games, "buffer" frames after a block animation allow the player to input a counter-attack; in racing games, corner-cutting detection windows extend the "on track" state by a frame or two. The universal form is: the physics simulation's boundary conditions (ledge, track edge, valid state) should trail the player's input window by a perceptually invisible amount.
- **Test for:** measure: tap jump exactly 1 frame after leaving a ledge (controls) → expected: no jump; tap within the coyote window → expected: jump executes. Parameterize the coyote window per game; 4–6 frames is a useful starting range.
- **Failure mode — No coyote time:** players consistently "miss" ledge jumps they visually feel they made. The game reads as unfair or unresponsive. Celeste without coyote time would be technically accurate but perceived as broken.

---

#### B.4 Additional Forgiveness Windows

**Rule:** Every high-frequency player action — jump, attack, dodge, interact — benefits from a small forgiveness window at its edges. Widen windows in the player's favor invisibly.

- **Celeste's documented forgiveness systems (✅ Thorson, 2020):**
  1. Coyote time (5 frames post-ledge)
  2. Jump buffering (pre-landing input stored)
  3. Jump corner correction (auto-nudge around corners on jump)
  4. Dash corner correction (pop up onto ledge on sideways dash)
  5. Half-gravity at jump apex (longer hangtime for landing adjustment)
  6. Lift momentum storage (platform velocity carries forward several frames after departure)
  7. Stamina refund grace window (converts expensive upward wall-jump to free directional jump within a grace period)
- **General form:** identify every action where timing-precision failure produces frustrating death/failure rather than intentional challenge. Insert a forgiveness window sized to be imperceptible but meaningful (typically 2–8 frames at 60 fps). Document each window explicitly; do not trust implicit engine behavior.
- **Test for:** play the game with "forgiveness off" (a debug mode that removes all windows). The difficulty spike shows exactly where forgiveness is doing work. Tune windows so the spike is a deliberate design choice, not an oversight.
- **Failure mode — Forgiveness creating unintended shortcuts:** an overly generous corner correction on a platformer with tight spatial puzzles can allow bypassing intended challenge. Test each window in the hardest level in the game, not just average levels.

**Procedural implication for B.1–B.4:** Forgiveness windows are a physics contract, not a level design contract. They must be centralized in the character controller, not implemented per-level by the generator. A generated level that requires tighter-than-designed forgiveness to complete is a malformed level and must be rejected by the validator.

---

### Sub-domain C — The Juice Toolkit

Covers: squash/stretch, anticipation/follow-through, screenshake, hit-stop/freeze frames, particles, tweening/easing, sound layering, camera kick.

**The overarching principle:** each primitive in the juice toolkit communicates a specific type of information. The toolkit is not additive decoration — it is a multi-channel communication system where each channel is redundant with at least one other. The Kao inverted-U applies per-scene: the total sensory load must stay in the Medium–High band.

---

#### C.1 Squash & Stretch

**Rule:** Deform objects along their direction of motion (stretch on acceleration, squash on impact) while preserving their apparent volume. This communicates mass, speed, and flexibility — the audience reads physics through shape.

- **Exemplar:** *Hollow Knight*'s Knight squashes slightly on landing and stretches slightly at jump peak. The deformation is 1–3 pixels on a small sprite — enough to read, not enough to distort hitbox. *Cuphead*'s rubber-hose style uses dramatic squash/stretch as genre expression — valid because the genre expectation is set from the first frame.
- **Thomas & Johnston source (✅):** "The squash and stretch gives the illusion of flexible movement... the key principle being that the volume remains constant." (*The Illusion of Life*, 1981)
- **Test for:** avatar and primary objects visually deform (however subtly) on high-velocity state transitions (takeoff, peak, landing, hit). Volume appears constant — the object doesn't shrink or grow, just changes shape.
- **Failure mode — Rigid feel:** no squash/stretch makes every object look like a physics box with art painted on. Landing animations feel like the character teleported to a stopped state.
- **Genre note:** turn-based games, visual novels, and menu-heavy games use squash/stretch on UI elements and dialogue portraits rather than avatar bodies. A button that squishes on press communicates "received input" even in the absence of a physical avatar.

---

#### C.2 Anticipation

**Rule:** Before any major action, show a brief preparatory movement in the opposite direction. This communicates intent and sets up the follow-through. On player avatars, keep anticipation minimal (it reads as input lag). On enemies and environmental objects, make it prominent (it signals a threat the player must read).

- **Exemplar (enemy):** Dark Souls / Elden Ring enemy wind-ups. Every dangerous attack has an anticipation phase (animation pulling back, glowing tell, vocal cue). The anticipation duration scales with attack damage — longer for more dangerous attacks.
- **Exemplar (player):** Sekiro (FromSoftware, 2019). Player attacks have minimal startup frames — they land quickly. The weight of the swing is communicated by follow-through and impact VFX rather than a slow wind-up.
- **Thomas & Johnston source (✅):** anticipation principle — "almost all actions are preceded by an anticipation of that action... it prepares the audience for a major action about to be performed."
- **Test for (enemy):** every enemy attack with damage above the median has a visible and distinct anticipation phase ≥ the player's reaction time minimum (typically 150–200 ms). Test by placing a new player in front of the attack; they should be able to respond having never seen it before.
- **Test for (player avatar):** player attack startup ≤ a responsiveness ceiling (typically 4–8 frames). Any longer reads as input lag.
- **Failure mode:** enemy with no anticipation = un-telegraphed attack = unfair damage. Player with long anticipation = slow controls = frustrating to use.

---

#### C.3 Follow-Through and Overlapping Action

**Rule:** After an action completes, parts of the character or object continue moving due to inertia. Secondary elements (hair, cape, weapon) continue moving after the primary body stops. This communicates that movement has real physical consequences.

- **Exemplar:** Hollow Knight's slash VFX outlasts the attack recovery so the player's controls return quickly while the visual weight of the hit is still readable. The follow-through is a VFX commitment, not a gameplay commitment.
- **Thomas & Johnston source (✅):** "Follow through and overlapping action: when the main body stops, accessories continue moving."
- **Test for:** identify all heavy actions (slam, land, power swing). Confirm that at least one secondary element (trail, VFX, cloth, debris) continues animating for 2–5 frames after the primary action resolves.
- **Failure mode — Dead stop:** every action terminates cleanly on the same frame; the world looks plasticky and weightless.

---

#### C.4 Hit-Stop / Freeze Frames

**Rule:** When an impact event occurs, freeze all affected entities for a brief duration (typically 3–10 frames / 50–167 ms at 60 fps). Duration scales with impact magnitude. Freeze both the attacker and the struck entity.

- **Source (✅ Sakurai, Famitsu #490–1, 2015, via Source Gaming):** "When you strike the opponent, both parties momentarily freeze... the more damage an attack inflicts, the longer the hitstop." For Smash Bros., projectiles get minimal hitstop; melee smash attacks get extended hitstop. Ryu's attacks in Smash received individually tuned hitstop to match Street Fighter II character.
- **Source (✅ critpoints.net / Celia Wagar, 2017):** Street Fighter II uses approximately 10 frames of hitstop. This extended the 2-in-1 cancel window because the hitstop froze both characters' states, giving players time to input the special move consistently.
- **Source (✅ Lin et al., 2022):** hit stop ranked among the top three impact feel features alongside sound coherence and camera control, derived from NLP analysis of 16 top-selling action games' player reviews.
- **Test for:** every impact above a defined force threshold triggers hitstop of at least 3 frames (50 ms). More powerful impacts get longer freezes (calibrated per event). Hitstop durations are stored in a per-event table, not computed dynamically from damage values alone.
- **Failure mode — No hitstop:** hits don't "land" perceptually; combat feels like slapping air. *Dark Souls II* is frequently cited as an example of weak impact feel relative to *Dark Souls* and *Elden Ring*, partly attributed to shorter/absent hitstop on many attacks.
- **Failure mode — Stacking hitstop on rapid-fire actions:** a rapid attack that fires hitstop on every frame of a combo creates the sensation of laggy controls. Cap cumulative hitstop per second to prevent this.

---

#### C.5 Screen Shake / Camera Kick

**Rule:** Offset the camera (or entire screen) briefly when a significant impact or event occurs. For 2D games, screen-space translation is standard. For 3D games, camera rotation is preferred over translation (translation can move the camera through walls). Use a trauma-based model: accumulate `trauma` on each hit, derive shake magnitude from trauma, decay trauma over time.

- **Source (✅ Nijman, INDIGO 2013 "The Art of Screenshake"):** demonstrated a side-scrolling shooter where adding screenshake on gunfire was one of the most immediately satisfying single changes out of approximately 30 iterative tweaks. The demo is the canonical illustration of a single juice primitive's isolated impact.
- **Source (✅ Borderline Games, trauma-based screenshake model):** `trauma` accumulates from hits; `shake = trauma^2` (non-linear so small trauma is nearly invisible, large trauma is dramatic); trauma decays at a fixed rate; shake offsets include rotation in 3D to avoid wall-clipping.
- **Source (✅ Lin et al., 2022):** camera control is one of three top-ranked impact feel features.
- **Test for:** (a) a significant impact event triggers at least 2 frames of visible camera displacement; (b) the displacement decays smoothly (not abruptly); (c) in 3D, the camera does not clip through wall geometry; (d) multiple rapid impacts produce an additive trauma increase, not a shake reset.
- **Failure mode — No screenshake:** impacts feel distant and unconvincing. Most commonly reported as "combat feels weak."
- **Failure mode — Per-hit reset shake:** each hit resets a counter rather than accumulating trauma, causing rapid hits to pulse unnaturally rather than building up and releasing.
- **Genre note:** Strategy and puzzle games use screenshake sparingly, typically only on rare, dramatic events (base destroyed, big combo, game-ending move). UI shake on a card game may signal a mistake (invalid play). The same trauma model applies — just calibrate low.

---

#### C.6 Particles

**Rule:** Spawn short-lived particle bursts on impact events, state changes, and reward moments. Particles communicate event type (hit, break, collect, die) and magnitude (more particles / larger burst = more significant event). Always budget total simultaneous particle systems — they are the primary source of juice-related performance degradation.

- **Exemplar:** Jonasson & Purho "Juice It or Lose It" (GDC Europe 2012) — demonstrated iterative addition of particles to a block-breaking game, showing how particle bursts on block destruction made scoring feel momentous where previously it was inert.
- **Exemplar:** Peggle (PopCap, 2007). The "Extreme Fever" sequence — triggered when the last orange peg is cleared — fires confetti, slow-motion, and Beethoven's Ode to Joy. This extreme particle/audio event is reserved for a once-per-level rare moment, exactly matching the rarity principle.
- **Test for:** (a) each distinct event type has a distinct particle signature (color, shape, count, duration) that does not visually collide with other event types; (b) particle budgets are capped per scene: maximum concurrent emitters, maximum particles per emitter, total screen coverage percentage; (c) particle density scales monotonically with event importance.
- **Failure mode — Undifferentiated particle soup:** all events emit the same white-spark burst; players cannot read what happened. Often occurs when polish is applied globally with one "splat" effect rather than per-event.
- **Failure mode — Performance-busting particle storms:** uncapped particle emitters on rapid-fire combat events can push frame time above 16.7 ms, breaking the feel of the game through latency rather than through aesthetics.

---

#### C.7 Tweening / Easing

**Rule:** Animate all non-instantaneous value changes using a non-linear easing function. Linear interpolation reads as mechanical and robotic. The most game-relevant easing curves:
- **Ease-out** (fast start, decelerating end): objects starting in motion and settling — UI panels sliding in, coins arcing to a counter, a character landing.
- **Ease-in** (accelerating start, fast end): objects beginning at rest and gaining speed — a rocket launching, a power-up activating.
- **Ease-in-out** (S-curve): camera movements, character running startup.
- **Overshoot/spring/back** (exceeds target, bounces back): reward confirmations, button presses, score popups — conveys energy and delight.

- **Source (✅ Robert Penner, "Tweening" / penner chapter 7):** foundational mathematical definitions of easing functions. Penner's equations are the industry standard, implemented in virtually every game engine and UI library.
- **Source (✅ multiple game-anim educators):** "Linear interpolations produce tasteless results when animating movements; good tweening boils down to a good application of the 12 principles of animation."
- **Test for:** identify all value transitions in the game (camera lerp, health bar drain, score accumulation, object spawn). Confirm none are linear (constant velocity). Document the chosen easing function for each transition type.
- **Failure mode — All-linear transitions:** health bars drain at constant speed (robotic), cameras snap without momentum (nauseating), score tickers tick identically for 1 point or 1,000 (no magnitude communication).
- **UI timing targets:** button hover/press: 100–200 ms; screen transition (fade/slide): 200–400 ms; tutorial reveal: 300–500 ms. Anything over 500 ms for a routine UI action reads as sluggish.

---

#### C.8 Sound Layering

**Rule:** Every player action with a physical outcome needs at least one immediate audio response. Complex or important actions get layered audio: low-frequency body (impact thump), mid-frequency texture (material sound), high-frequency sizzle (crackle, air displacement). Pitch and volume variation prevents listener fatigue from repeated sounds.

- **Source (✅ Juicy Audio paper, ACM NIME 2023, "Juicy Audio: Audio Designers' Conceptualization of the Term in Video Games"):** documents how audio designers define and implement juiciness — layering, pitch randomization, dynamic mixing, and audio-visual coherence.
- **Source (✅ Lin et al., 2022):** "sound coherence" — alignment of audio with visual feedback timing — was one of three top-ranked impact feel features.
- **Examples (✅ multiple sources):**
  - Pac-Man's "waka-waka" pellet sound — simple but perfectly timed to the eating action
  - Super Mario Bros. coin chime — pitch-tuned to feel rewarding, never fatiguing in hundreds of repetitions
  - Nuclear Throne (Vlambeer, 2015) — layered gunfire: body, report, shell casing, enemy hit all composited together, with pitch variation per shot
- **Test for:** (a) mute all sound and confirm the game is still playable and the UI is still navigable (i.e., sound augments, does not substitute for visual feedback); (b) play with only sound and confirm you can identify every distinct event type by audio signature alone; (c) fire the same action 20 times rapidly — does the sound feel different enough on each instance, or does it read as identical repetition?
- **Failure mode — One sound per event, no variation:** after 10 repetitions, the player's brain habituates and the sound provides no new information. In a game where the player fires 100 bullets per minute, a single gunshot sample at fixed pitch becomes invisible.
- **Failure mode — Audio-visual desync:** sound fires 50+ ms before or after the visual event it corresponds to. Even non-musicians notice; it breaks the illusion of physical causality.

---

#### C.9 The Inverted-U Caution (Mandatory Synthesis)

**Rule:** Apply a per-scene, per-action-type "feedback budget" with a hard upper ceiling. The ceiling is derived from the Kao study's finding that the Extreme condition degrades every measured outcome. A practical budget ceiling: at any given moment, no more than Medium–High juice density as defined by the number of simultaneous feedback channels (animation + shake + particles + sound + hitstop + UI effects).

- **Source (✅ Kao et al., 2020, N=3,018):** "Both Medium Juiciness and High Juiciness outperform No Juiciness" and Extreme Juiciness across play time, player experience, intrinsic motivation, and performance.
- **Source (✅ Kao et al., CHI 2024, N=1,699):** amplification backfires when it impairs the player's sense of agency. The mechanism: too much feedback obscures game state, making the player feel controlled by the game rather than in control of it.
- **Practical implementation:**

```
per-event feedback budget = {
  simultaneous_particle_emitters: max 3–5,
  peak_shake_amplitude_px: calibrated by event tier (minor → 2 px, major → 8 px, rare/climactic → 20 px),
  hitstop_frames: 0–10 (none for weak hits, up to 10 for rare climactic moments),
  simultaneous_sfx_layers: max 3 (low/mid/high),
  duration_until_screen_clear: 0.5–1.5 s
}
```

- **Test for:** a debug overlay that shows the current summed feedback load (particle count, shake magnitude, active audio voices) in real time. Trigger every event type simultaneously and confirm the peak does not exceed the High tier ceiling.
- **Failure mode — Uncapped juice escalation:** generators or designers who "add more effects to important moments" without a ceiling produce Extreme-tier feedback at the climax, which Kao et al. shows is *worse* than Medium.

**Rarity ladder — reserving the biggest effects:** Reserve the top tier of each feedback primitive (maximum shake, maximum hitstop, maximum particles, dramatic slow-motion) for events that occur at most once per session. These are: boss kills, final-level completion, rare loot acquisition, first encounter with a new world mechanic. Every additional use at that tier degrades its impact through habituation.

---

### Sub-domain D — The 12 Principles of Animation Applied to Games

**Foundation texts:** Frank Thomas & Ollie Johnston, *The Illusion of Life: Disney Animation* (1981); Chris Totten, "12 Principles for Game Animation" (Medium); Jonathan Cooper, *Game Anim* (2019).

The 12 principles were developed for film but apply to real-time interactive systems with the game-specific adjustments noted.

| # | Principle | Game-specific application | Failure mode |
|---|-----------|--------------------------|--------------|
| 1 | **Squash & Stretch** | Deform avatars and objects on velocity state changes; preserve hitbox volume even when art stretches. In small sprites (16–32 px), limit to 1–3 pixels. | Rigid, weightless objects; hitbox/sprite desync that players exploit or complain about. |
| 2 | **Anticipation** | **Invert the asymmetry:** enemy anticipation = player-readable warning (good); player-avatar anticipation = input lag (bad). Minimize player startup; maximize enemy wind-up. | Enemy attacks with no wind-up feel unfair. Player attacks with long startup feel sluggish. |
| 3 | **Staging** | UI clarity: most important information in highest-contrast region. Combat readability: enemy attack tells are on-screen and unoccluded. HUD hierarchy matches moment-to-moment priority. | Critical information (health, incoming attack) buried under VFX or in low-contrast regions. |
| 4 | **Straight Ahead vs. Pose to Pose** | Pose-to-pose is the game-relevant model: define key states (idle, attack, hit-react, dead) and animate transitions between them. The "in-between" is the engine's interpolation, not the animator's frame-by-frame work. | Poorly defined pose-to-pose keys produce unreadable transitions; enemy attacks become ambiguous. |
| 5 | **Follow-Through & Overlapping Action** | Secondary elements (hair, cape, weapon arc, VFX trail) continue for 2–5 frames after primary action resolves. This communicates weight without extending recovery frames. | All motion stops simultaneously → plasticky, weightless. Common in early 3D games. |
| 6 | **Slow In & Slow Out** | Ease-out on arrivals (avatar landing, UI panel settling). Ease-in on departures (jump launch, UI panel leaving). Apply to all value transitions; linear is almost never correct. | Linear value changes read as mechanical and robotic. Health bar draining at constant speed has no drama. |
| 7 | **Arcs** | Natural movement follows arcs, not straight lines (except mechanical/robotic characters). Jump trajectories, sword swings, thrown projectiles should arc. Straight-line projectile travel can feel robotic unless that's the aesthetic (laser, railgun). | Straight-line jumps or attacks feel mechanical even when the physics are technically correct. |
| 8 | **Secondary Action** | Movement that reinforces (not competes with) the main action. Character running → arm swings. Character idle → breathing, eye blinking, small weight shifts. These are optional; cut if performance budget is tight. | Over-animated secondary actions compete for player attention with the primary game state. |
| 9 | **Timing** | Frame count defines pace: faster attacks feel aggressive; slower attacks feel weighty. Timing must be consistent per attack type so players can learn the rhythm. This is also the game's "grammar" — timing consistency enables mastery. | Inconsistent timing (same attack varies by 2–4 frames randomly) makes mastery impossible and reads as buggy. |
| 10 | **Exaggeration** | Amplify physical effects beyond realism for communicative clarity. A sword swing doesn't need to exactly simulate a steel blade; it needs to communicate "I hit you hard." Scale exaggeration to genre tone: a grim survival game uses subtle exaggeration; an arcade game uses broad exaggeration. | Hyper-realistic physical accuracy often reads as weak or dull. Exaggeration is not a flaw; it is a design choice. |
| 11 | **Solid Drawing** | Translate to: consistent character silhouette across all animation states. Players read character type by silhouette, not texture. Every state change (idle, attack, hurt, dead) must preserve the character's readable silhouette. | Silhouette-destroying animations (character contorts so extreme the form is unreadable) break player targeting and identity. |
| 12 | **Appeal** | Characters must be interesting to look at — not necessarily "nice." Believable, internally consistent characters read as real to the player. Appeal is genre-dependent: *Cuphead*'s appeal is cartoon; *Dark Souls*' appeal is grotesque but coherent. | Generic, interchangeable characters that don't feel like coherent entities. Procedural character generation that averages across types destroys individual appeal. |

**Critical game inversion (✅ established by multiple game-animation educators):** Principle 2 (Anticipation) must be **selectively applied**. In film, all characters benefit from anticipation. In games, player avatar anticipation reads as input lag. The asymmetry — anticipation for enemies, minimal startup for players — is the single most game-specific departure from the film principles and must be explicitly coded into any game animation specification.

**Procedural implication:** Animation states for generated content (enemies, NPCs, objects) should be authored as a fixed library of `(anticipation, active, recovery)` triplets per action type. Generators select from this library; they do not compute new animation parameters. The timing principle demands consistency — the same attack type across all generated enemies must use the same timing signature.

---

### Sub-domain E — Camera & Feel

#### E.1 The Camera as a Feel Primitive

**Rule:** The camera is a feedback device, not merely a framing tool. Its movement communicates force, speed, and weight. Camera lag (the camera trails the avatar slightly) creates a sense of momentum. Camera kick on impacts communicates force. Camera lookahead (the view leads in the player's direction) creates a sense of agency and awareness.

- **Source (✅ Nijman, "Art of Screenshake," INDIGO 2013):** adding camera shake to gunfire was one of the highest-perceived-impact single changes in the live demo.
- **Source (✅ Lin et al., 2022):** camera control ranked in the top three impact feel features.
- **Exemplar (camera lag):** Super Mario World (Nintendo, 1990). The camera shifts in the direction Mario faces, giving extra lookahead space and communicating directional intent. The camera's soft-follow (lagging slightly behind Mario's position) gives a sense of Mario having real momentum.
- **Test for:** (a) the camera does not move at pixel-exact lockstep with the avatar (unless the game is a deliberate screen-lock style); (b) camera transitions (from idle to moving, to stopping) use eased interpolation, not snap; (c) impact events trigger a measurable camera offset that decays smoothly.
- **Failure mode — Locked camera with no give:** the camera snaps to center the avatar every frame. Motion feels like moving a cursor on a painting, not inhabiting a body in a world.
- **Failure mode — Excessive camera lag:** the avatar runs off the visible area before the camera catches up. Common in early platformers; creates confusion about what's ahead.

#### E.2 Screenshake Implementation

See C.5 above. Key additions for 3D contexts:

**Rule (3D):** Rotate the camera rather than translating it for screenshake. Translation moves the camera through world geometry, causing wall-clipping and pop-through. Rotation keeps the camera in place while still providing a perceptual "hit" sensation.

- **Source (✅ Borderline Games, trauma-based screenshake):** rotation-based shake for 3D; `trauma` accumulator model.
- **Test for:** in 3D, fire maximum-intensity shake in a corridor. Confirm the camera does not clip through any wall.

#### E.3 Platform-Specific Camera Techniques

| Technique | Definition | Use case | Risk |
|-----------|-----------|----------|------|
| **Deadzone** | Camera only moves when avatar reaches edge of a central region | Prevents jitter during subtle movement | Deadzone too large → avatar disappears at edges |
| **Lookahead** | Camera offsets in movement direction | Provides spatial intelligence to player | Lookahead too aggressive → camera races ahead before player commits |
| **Edge snapping** | Camera snaps cleanly to room/screen edges | Puzzle games, classic side-scrollers | Snap is instantaneous → jarring transition |
| **Platform snapping** | Camera ascends with jumps but descends slowly, revealing falls | Platformers | Asymmetric speed makes descents feel longer than intended |
| **Boss lock** | Camera locks to an arena or tracks both player and boss | Boss encounters | Lock removes the player's spatial agency if the arena is too large |

---

### Sub-domain F — Feel of UI / Menus

**Rule:** Every interactive UI element benefits from juice. Buttons should respond to hover and press states with animation and audio. Menu transitions should use eased animations, not cuts or linear slides. Every interaction should provide multimodal confirmation (visual + audio).

- **Source (✅ multiple UI/UX educators; Juicy UI, Medium 2023):** hover state animation ~200 ms; press state squish; audio click on interaction.
- **Source (✅ Peggle, 2007):** the "Extreme Fever" sequence applies game-juice principles to a UI moment (the post-level screen), with fireworks, slow-motion, and orchestral music. It demonstrates that menu/UI juice can be as impactful as in-game juice.
- **Source (✅ Bejeweled, 2001; Candy Crush):** cascading match sounds, escalating pitch on combo, sparkling explosion animations on gem destruction — all applied to pure UI interactions (no physical avatar).

#### F.1 Button and Interactive Element Feel

**Rule:** Buttons should:
1. Respond to hover with a visual change (scale 1.02–1.05×, color shift, glow) in 100–200 ms.
2. Respond to press with a squish (scale 0.95×) or depression animation in < 100 ms.
3. Emit an audio click or chime on press.
4. Confirm navigation (menu scroll, item selection) with a distinct audio signature separate from confirmation (select / back).

- **Test for:** play through the entire main menu and all sub-menus with eyes closed. Can you tell: (a) which element is highlighted? (b) when a selection is confirmed? (c) when you navigate back? If not, sound design is missing.
- **Failure mode — Silent menus:** no audio feedback on navigation. Players feel blind; accessibility suffers significantly.

#### F.2 Screen and State Transitions

**Rule:** Transitions between game states (menu → gameplay, level complete → result screen, death → respawn) should use eased animations (fade, slide, scale) of 200–500 ms. Faster = energetic; slower = ceremonial.

- **Exemplar:** Hollow Knight's loading transitions (Vigil torch wipes, fade-to-black with ambient sound) are deliberate, easing the psychological shift between environments. Death respawn uses a faster, more energetic transition matching the game's fast retry loop.
- **Test for:** time all transitions. Flag any under 100 ms (feels like a glitch) or over 600 ms for routine actions (feels like loading, even if nothing is loading).
- **Failure mode — Instant state changes:** the game "cuts" between states like a video editor's hard cut. This breaks the player's sense of continuous inhabited world.

#### F.3 Reward and Celebration Moments in UI

**Rule:** Apply high-juice feedback to rare, positive UI events: level complete, achievement unlock, new high score, collectible completion. Use the same rarity-ladder principle: reserve your most spectacular effect for the rarest event.

- **Exemplar:** Peggle's Extreme Fever is the canonical example — the final clear triggers confetti, slow-motion, and Beethoven's Ode to Joy, a response wildly disproportionate to "clearing a level" but calibrated to the emotional moment.
- **Test for:** create a list of all "celebration moments" in the game. Confirm each has a distinct juice level that scales with its rarity and importance. Confirm the highest-tier celebration fires at most once per session.
- **Failure mode — Flat reward screens:** all achievements produce the same "Level Complete" banner with no differentiation. Players cannot feel the difference between a routine clear and a perfect clear.

**Procedural implication for F.1–F.3:** UI juice is centralizable. A generated game's UI should draw from a shared component library (button, panel, transition, reward screen) whose feel parameters are hand-tuned once and reused consistently. The generator should not create new UI component variants; it selects from the library and composes them. This ensures all generated games feel polished at the UI layer regardless of content generation quality.

---

## 4. Recommendations

**Stage 1 — Foundation (ship before any content generation):**
1. Measure total input-to-pixel latency on target hardware. Establish the baseline. Target ≤ 50 ms for action genres.
2. Implement input buffering and coyote time in the character controller as configurable parameters (not hardcoded frame counts). Document each forgiveness window in the game design document.
3. Define the physics constants (mass, friction, gravity, jump curve, speed) in a single configuration file. Lock them as version-controlled constants.
4. Author the "per-action feedback budget table" — a spreadsheet or config file mapping (event type × magnitude tier) to (hitstop frames, shake amplitude, particle emitter count, audio layers). Establish the min and max guardrails from the Kao Medium–High band before any effects are implemented.

**Stage 2 — Core juice layer (implement on a feature-complete, playable build):**
5. Implement hitstop on all impacts above the defined force threshold. Calibrate by event table.
6. Implement the trauma-based screenshake model. Test in 3D: confirm no camera-wall clipping.
7. Implement squash/stretch on avatar state transitions (jump launch, peak, landing, hit).
8. Layer sound on every player action: minimum one immediate audio response; three layers (low/mid/high) for primary combat or interaction events.
9. Apply easing to all value transitions (health bars, camera, score counters, UI panels). Eliminate all linear interpolation.

**Stage 3 — UI juice and celebration moments (ship polish pass):**
10. Add hover/press animation and audio to all interactive UI elements.
11. Define the rarity ladder for celebration moments. Author high-juice reward screens for the top 3 rarest player achievements.
12. Test the full juice budget: run the debug overlay showing simultaneous effects load. Confirm no single scene exceeds the High-tier ceiling from the budget table.

**Stage 4 — Procedural-readiness validation:**
13. Build the feedback-budget linter: a scene validator that ingests the budget table and rejects any generated scene exceeding the ceiling.
14. Build the physics-contract validator: confirms generated levels do not require tighter-than-designed forgiveness windows to complete.
15. Build the animation-state validator: confirms all generated enemies and objects draw exclusively from the authored animation library, never computing new timing parameters.

**Thresholds that change the plan:**
- If targeting competitive/esports genre: tighten the latency target to ≤ 30 ms controller-to-pixel; bring in dedicated input latency hardware testing at the beginning of Stage 1.
- If targeting mobile: haptics replace some screenshake and audio feedback; budget for screen-space effects separately from CPU/GPU budget (mobile GPU particle count ceilings are much lower).
- If the game has no physical avatar (pure strategy, card, board): remove Stage 1 coyote time / input buffering work; replace with per-turn confirmation animations and sound. The Kao inverted-U still applies to board/card game particle effects.
- If hand-authored content (not procedural): remove Stage 4 validators; replace with a playtest protocol where a junior playtester attempts every interaction blindfolded (eyes closed) and narrates what they think happened.

---

## 5. Caveats

**Strict vs. popular definition of "game feel."** Celia Wagar (critpoints.net, 2020) argues correctly that popular presentations conflate game feel (Swink's precise definition: real-time control in simulated space + polish) with general game design quality. Nijman's "30 tweaks" include changes to enemy HP, gun fire rate, and bullet size — which are mechanical design changes, not feel changes in Swink's sense. This document uses "game feel" broadly (covering the full experience of moment-to-moment play) but distinguishes feel (latency, forgiveness, physics) from juice (audiovisual amplification) from design (mechanics, difficulty, systems).

**The Kao study's generalizability.** The N=3,018 study was conducted on a single action RPG with a specific visual style. The inverted-U finding (Extreme juice hurts) is directionally robust and consistent with the CHI 2024 follow-up and with practitioner reports. However, the specific breakpoints (what counts as "Medium" vs. "High" vs. "Extreme") are not transferable across genres with different visual vocabularies. A bullet-hell shmup has a different juice baseline than a text-based RPG. The Kao result constrains the ceiling; calibrating the specific ceiling requires per-genre and per-title judgment and playtesting.

**Animation principles were developed for film.** The 12 Disney principles were created for non-interactive, pre-rendered content. In games, timing is dynamic (not fixed by a director), follow-through competes with player-controlled recovery, and anticipation's meaning inverts on player characters. All applications cited in this document are adapted interpretations, not direct applications. Chris Totten's "12 Principles for Game Animation" is the best game-specific adaptation of these principles.

**Latency thresholds are interaction-type-dependent, not genre-dependent alone.** Raaen's survey groups thresholds by genre, but individual interaction types within a genre vary. In an RTS, clicking a unit has a 100 ms-or-better feel requirement; issuing a move order to a selected unit tolerates 500 ms. The genre-level thresholds are starting points for design; final validation requires per-interaction measurement.

**Hand-authoring of the feedback budget table is required.** This document specifies a "per-action feedback budget table" as the procedural mechanism for consistent juice. That table must be hand-authored by a designer with playtesting; it cannot itself be procedurally generated without circular dependency. The table is the single artifact in this skill's implementation that must come from a human (or from a deeply constrained, human-validated AI) before any generated content can be evaluated against it.

---

## Relationship to `combat-design` Skill

**This skill is upstream of `combat-design`.** `game-feel-and-juice` establishes the universal principles — real-time control, simulated space, polish, the juice toolkit, the inverted-U ceiling, the 12 animation principles, camera feel, and UI feel. These apply to any game, any genre, any action.

`combat-design` applies this foundation specifically to the combat context: impact feedback in hitting and being hit, hit-stop calibration for combat pacing, readability and telegraphing (anticipation on enemies = fairness), enemy and encounter design, lethality systems, and the specific interactions between hitstop duration and combo-cancel windows. The feedback-budget principle is restated in `combat-design` with combat-specific values (hitstop per hit tier, shake per damage tier), but the underlying inverted-U and budget-table methodology come from this skill.

**Practical rule for the AI agent:** when designing or evaluating any game, apply `game-feel-and-juice` first (does the moment-to-moment control feel correct? does the juice budget stay in the Medium–High band? are animation principles applied?). Then, if the game has combat, layer `combat-design` on top (are attacks telegraphed? does hitstop scale with damage? is the danger-cue vocabulary consistent?). The skills do not overlap in their rules; `combat-design` cites upward to this skill's foundation, not the reverse.

---

## Sources

Primary sources:

- Swink, Steve. *Game Feel: A Game Designer's Guide to Virtual Sensation*. Morgan Kaufmann, 2009. ISBN 9780123743282.
- Thomas, Frank, and Ollie Johnston. *The Illusion of Life: Disney Animation*. Disney Editions, 1981.
- Nijman, Jan Willem. "The Art of Screenshake." INDIGO Classes lecture, Control Conference, 2013. YouTube: https://www.youtube.com/watch?v=AJdEqssNZ-U
- Jonasson, Martin, and Petri Purho. "Juice It or Lose It." GDC Europe, 2012. YouTube: https://www.youtube.com/watch?v=Fy0aCDmgnxg · GDC Vault: https://www.gdcvault.com/play/1016487/Juice-It-or-Lose
- Kao, Dominic. "The Effects of Juiciness in an Action RPG." *Entertainment Computing* 34 (2020). Improbable Research summary: https://improbable.com/2020/06/01/the-effects-of-juiciness-in-an-action-rpg-new-study/
- Kao, Dominic et al. "How does Juicy Game Feedback Motivate? Testing Curiosity, Competence, and Effectance." CHI 2024. https://dl.acm.org/doi/10.1145/3613904.3642656
- Sakurai, Masahiro. "Thinking About Hitstop." Famitsu column #490–1, 2015. Translated by Source Gaming: https://sourcegaming.info/2015/11/11/thoughts-on-hitstop-sakurais-famitsu-column-vol-490-1/
- Thorson, Maddy. "Celeste & Forgiveness." Medium, 2020. https://maddythorson.medium.com/celeste-forgiveness-31e4a40399f1
- Lin, Duan, Wen, and Cai. "What Features Influence Impact Feel? A Study of Impact Feedback in Action Games." IEEE GEM 2022. arXiv: https://arxiv.org/pdf/2208.06155
- Pichlmair, Martin, and Mads Johansen. "Designing Game Feel. A Survey." arXiv 2011.09201 (2020). https://arxiv.org/abs/2011.09201
- Hicks, Kieran, Patrick Dickinson, Jussi Holopainen, and Kathrin Gerling. "Good Game Feel: An Empirically Grounded Framework for Juicy Design." DiGRA 2018. https://dl.digra.org/index.php/dl/article/view/936
- Raaen, Kjetil. "Latency Thresholds for Usability in Games: A Survey." NIKT 2014. https://www.ntnu.no/ojs/index.php/nikt/article/view/5252
- Wagar, Celia. "Hitstop/Hitfreeze/Hitlag/Hitpause/Hitshit." critpoints.net, 2017. https://critpoints.net/2017/05/17/hitstophitfreezehitlaghitpausehitshit/
- Wagar, Celia. "You Don't Know What Game Feel Is, Read the Damn Book Please!" critpoints.net, 2020. https://critpoints.net/2020/05/23/you-dont-know-what-game-feel-is-read-the-damn-book-please/
- Totten, Chris. "12 Principles for Game Animation." Medium. https://totter87.medium.com/12-principles-for-game-animation-a9137ef44345
- Penner, Robert. "Tweening." Chapter 7, *Programming Macromedia Flash MX*. https://robertpenner.com/easing/penner_chapter7_tweening.pdf
- Borderline Games. "All Purpose Screenshake, the Right Way." http://blog.borderline.games/tutorials/gettinghit!/trauma-based-screenshake.html
- England, Liz. "Review: Game Feel by Steve Swink." https://lizengland.com/blog/review-game-feel-by-steve-swink/
