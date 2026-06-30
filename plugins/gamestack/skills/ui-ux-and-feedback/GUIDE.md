# UI/UX & Information Design — Guide

Information architecture and feedback design: what to show, when, in which channel, and how to keep the total cognitive load within the player's working-memory budget. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Fagerholt & Lorentzon's Chalmers thesis (2009); Hodent's *The Gamer's Brain* (2017); Norman's *The Design of Everyday Things* (1988/2013); Sweller's Cognitive Load Theory (1988); the CHI 2019 juiciness study; arxiv impact-feel study 2208.06155; GDC talks by Dino Ignacio (Dead Space UI, 2013) and Ahmed Salama (Minimal HUD Paradox); Naughty Dog's accessibility blog (2020); and the Housemarque/Returnal PlayStation Blog (2021). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested source. Full citations in the research doc (`docs/research/round1-universal/U4`).

> **The central law:** *show only what the player needs to decide right now.* Classify every HUD element by access frequency; surface Tier-1 state (death-relevant, per-second) permanently, Tier-2 on context, Tier-3 in menus. Every extraneous element is cognitive tax — Sweller's *extraneous load* — that directly degrades performance on the core task.

> For the feel of individual UI elements (squash on button press, easing, transition timing) see `game-feel-and-juice`. For visual contrast, signal color, and pixel-level legibility see `art-direction-and-readability`. For teaching mechanics during first play see `onboarding-and-teaching`.

---

# Sub-domain A — The Fagerholt–Lorentzon Four-Type Framework

> Fagerholt & Lorentzon (2009), *Beyond the HUD — User Interfaces for Increased Player Immersion in FPS Games* (Chalmers University of Technology): two binary axes — *fiction* (can characters within the world perceive this element?) and *geometry* (does it live in 3D space?) — produce four canonical UI types. Virtually every shipped game is a hybrid; choose each element's type deliberately. ✅ (Semantic Scholar indexed; ACM/CHI cited independently; repeated across GDC UI talks.)

| Type | Fiction | Geometry | Canonical example |
|---|---|---|---|
| **Diegetic** | Yes | Yes | Dead Space suit-spine health strip |
| **Non-diegetic** | No | No | WoW health bar overlay |
| **Spatial** | No | Yes | Left 4 Dead teammate outlines through walls |
| **Meta** | Yes (implied) | No | GTA V intoxication blur |

## A.1 Default to non-diegetic for complex, fast-changing data; reserve diegetic for data that gains dramatic weight in-world

Non-diegetic elements (2D overlay) iterate fastest, read clearest on all screen sizes, and carry zero production debt. Use them for ammo count, cooldown timers, objective markers, and inventory UI. Reserve diegetic for elements where the in-world presentation is *more fitting* than an overlay — not merely cooler. ✅ (Fagerholt & Lorentzon 2009; GDC 2013, Dino Ignacio on Dead Space.)

- *Dead Space* (Visceral Games, 2008): ammo projects as a hologram on the raised weapon; health is a light strip on Isaac's suit spine — both *more frightening* than an overlay in survival-horror context. ✅ GDC 2013, Ignacio; Medium, Ardeni.
- *World of Warcraft*: 40-player raid UI with buff stacks, cooldowns, and threat meters — non-diegetic because the data volume is architecturally incompatible with diegetic embedding. ✅ Fagerholt & Lorentzon cited exemplar.
- **Test for:** Can you read the element clearly from a 32" 1080p display at 3 meters? If diegetic placement sacrifices legibility in fast gameplay, it fails its purpose.
- **Failure mode — "Diegetic theater":** the team commits to in-world UI for immersion, discovers the information is unreadable in action, and ships an emergency non-diegetic overlay alongside the unfinished diegetic system — two competing, half-complete layers. ✅ Salama 2023, "The Minimal HUD Paradox."

## A.2 Use spatial UI for world-grounded signposting that characters don't need to acknowledge

Floating waypoints, enemy health bars in-world, interaction prompts above objects — 3D-placed, fiction-silent. Prefer spatial over non-diegetic when the alternative forces the player to context-switch between screen layers. ✅ (Fagerholt & Lorentzon; Nastyrodent.com framework.)

- *Left 4 Dead* (Valve, 2008): player-only teammate outlines through walls — world-positioned, no fictional claim, prevents friendly-fire confusion. ✅ Nastyrodent.com.
- **Test for:** Does removing the spatial element cause players to miss navigation or critical state in >5% of observed test sessions?
- **Failure mode — Narrative incoherence:** spatial UI in a photorealistic environment that the fiction cannot explain (a glowing arrow floating above a door the character would not see) is *worse* than a non-diegetic overlay — it adds visual noise and breaks narrative coherence simultaneously.

## A.3 Use meta UI for emotional/physical state; budget vestibular and perceptual impact

Screen vignettes, blood on lens, near-death cracks, speed blur — these represent the character's subjective experience as a 2D effect. Strong emotional weight, low production cost. Every meta effect must have a player opt-out (accessibility requirement; see Sub-domain E). ✅ (Fagerholt & Lorentzon; PlayStation Blog 2021.)

- *Returnal* (Housemarque, 2021): on critical health, HUD visor glass cracks with glitch animation, suit warnings fire, audio and haptic join — a multi-channel meta cascade that reads state without breaking immersion. ✅ PlayStation Blog 2021.
- *The Last of Us Part II*: red screen flash on damage; environmental desaturation at low health. ✅ Andy's Cabin Games, 2021.
- **Test for:** Does the meta effect help the player understand state faster than without it? Does it introduce photosensitivity or vestibular issues?
- **Failure mode — Obscured geometry:** a full-screen red flash at the moment an enemy attacks makes the player *less* able to dodge — the meta UI actively undermines the action it's communicating.

**→ Procedural / headless implication.** When generating a HUD spec from a mechanic set, classify each state variable on both Fagerholt axes first. Ask: (1) Does a plausible in-world object hold this data? (2) Is there authored 3D geometry for it? If both yes → candidate for diegetic. If the data changes faster than world-object animation can track → non-diegetic. If it describes how the player *feels* → meta. A roguelike or procedural sandbox almost always defaults to non-diegetic + meta: diegetic and spatial UI require authored anchors that generation cannot guarantee. Hybrid: make the *type* of data diegetic (health as visible wound state on the character model) while exposing the *number* non-diegetically.

---

# Sub-domain B — Information Hierarchy & Cognitive Load

> Sweller (1988): working memory holds ~3–7 items simultaneously; *extraneous cognitive load* — anything about the interface rather than the game task — is a reducible tax the designer owes the player. Hodent (2017) applies this directly: *"game workload must focus on the core experience; menus, icon decoding, and HUD clutter are all extraneous load."* ✅ (Sweller 1988 via ScienceDirect; Hodent 2017, *The Gamer's Brain*.)

## B.1 Tier state by access frequency; place highest-frequency elements at the periphery

Tier-1 (checked every second: health, ammo, current objective) → permanent HUD, corner positions, peripheral vision. Tier-2 (per-encounter: cooldowns, minimap, stamina) → context-triggered, fade after event. Tier-3 (per-session: stats, crafting, extended inventory) → menus only. Never put Tier-3 data on the permanent HUD. ✅ (GDC UX eye-tracking data via Polydin.com; *Breath of the Wild* HUD layout.)

- *The Legend of Zelda: Breath of the Wild*: hearts, stamina wheel, and equipped items in lower corners — permanent. Map: menu only. Weapon durability: inventory only. ✅ Sunstrikestudios; Justinmind HUD analysis.
- **Test for:** Can a playtester accurately report their current health, ammo, and objective *without pausing* while navigating a difficult encounter?
- **Failure mode — "Information anxiety":** the player pauses every 30 seconds to check state they cannot read during play. The HUD gives the illusion of information while failing to deliver it in context.

## B.2 Apply progressive disclosure: surface a system's UI only when the player first encounters that system

Do not show a mechanic's UI before the player has encountered the mechanic. Introduce one system at a time; after teaching, make the reference available but not forced (revisitable help menu, contextual hints). Front-loading all system information before play is Sweller's "extraneous load" maximized. ✅ (Hodent 2017; *The Last of Us* original HUD design.)

- *The Last of Us* (Naughty Dog, 2013): HUD completely disabled between meaningful moments; controls surface inline with action. ✅ Kotaku, Neonakis interview 2014.
- *Super Mario Bros.*: teaches running (empty right half), jumping (first Goomba), power-up (first ?-block) — no text, no HUD tutorial, pure environmental sequencing. ✅ Appcues onboarding analysis.
- **Test for:** Does a new player spend any time reading tooltip walls or static instruction screens during the first 5 minutes? If yes, that content belongs in play-time discovery.
- **Failure mode — Tutorial front-loading:** 12 mechanics explained in sequence before the player touches controls. Retention research: players forget material lacking emotional context or repetition. ✅ Hodent 2017: *"Players forget material lacking emotional significance or repetition."*

## B.3 Every icon must communicate intent without a tooltip — or carry a tooltip

An icon passes when ≥80% of representative playtesters can correctly describe its function without seeing a label. Icons that fail this threshold must be redesigned, not labeled. Labels are a fallback for an icon that failed, not a design solution. ✅ (Hodent UX testing protocol; Celiahodent.com.)

- Hodent's icon test: one icon per survey page; ask participant to describe form and function; compare against design intent. Misaligned = redesign. ✅ Celiahodent.com.
- **Test for:** Blind icon test with ≥43 representative participants — does intent match ≥80% of responses?
- **Failure mode — "Icon soup":** a toolbar of abstract symbols that require hover-tooltip on every use. Every encounter with the interface burns extraneous cognitive load. Common in strategy and RPG games where designers assume icon memorization.

## B.4 Separate primary, secondary, and tertiary layers with visual weight — not just position

Use size, contrast, color intensity, and motion to encode information priority. Primary state must dominate at a glance. Secondary state must recede visually. Tertiary state must leave the screen until summoned. Never give secondary and primary elements equal visual weight. ✅ (Hodent 2017; PlayStation Blog 2021, *Returnal*.)

- *Returnal*: critical data near the reticle; non-essential at periphery. Emergency state triggers a multi-channel escalation (crack + vignette + audio + haptic). ✅ PlayStation Blog 2021.
- Hodent color principle: red draws attention but loses effectiveness through overuse — restrict red to critical feedback (damage, low health), orange for warnings. ✅ Celiahodent.com.
- **Test for:** At 50% screen brightness, in active combat, can a playtester identify their health status within 0.5 seconds?
- **Failure mode — "Uniform grey soup":** all HUD elements at equal contrast and size; the eye must scan the entire screen to locate state.

**→ Procedural / headless implication.** When generating a HUD spec from a mechanic set, enumerate all player-facing state variables. Assign each a tier: if the variable can cause player death or critical failure without a 3-second reaction window, it is Tier 1. Tier-1 → permanent HUD, corners. Tier-2 → context-triggered. Tier-3 → menu. For AI-generated levels (roguelikes, procedural worlds): the HUD tier assignment is a constant design contract independent of content — the generator varies layout, never the HUD tier.

---

# Sub-domain C — Feedback & Game-State Communication

> Every player action that changes game state must produce immediate, multi-channel feedback synchronized within 15ms audio-visual. Feedback is information, not decoration: hit-stop communicates weight, screen shake communicates severity, particle burst communicates event type, audio confirms causality. The CHI 2019 juiciness study confirms the inverted-U: medium juice outperforms both extreme and none. ✅ (arxiv 2208.06155; CHI 2019 juiciness study; Hodent 2017.)

## C.1 Every state-changing action must produce immediate visual + audio feedback, synced within 15ms

A player action produces a consequence. That consequence must be communicated through at minimum one visual signal and one audio signal, synchronized within 15ms. Haptic amplifies conviction where platforms support it. Failure to communicate state change breaks the action-consequence loop and makes the game feel unresponsive regardless of actual input latency. ✅ (arxiv 2208.06155: A-V sync below 15ms critical; desync above 80ms rated significantly worse.)

- *Returnal*: weapon charge uses a rising audio cue resolving with a distinct haptic pulse; malignant items emit a heartbeat haptic while nearby. Four channels — one state change. ✅ PlayStation Blog 2021.
- **Test for:** Play with display off (audio only). Can you tell when you hit an enemy, when you were hit, and when you killed? If no, audio feedback is insufficient.
- **Failure mode — "Silent hit syndrome":** melee or projectile connects; no audio, no camera response, no particle. Players perceive the game as unresponsive at 60fps. Common in early prototypes and jam submissions.

## C.2 Use screen shake, hit-stop, and flash as semantic signals scaled to impact magnitude

Camera shake communicates severity. Hit-stop communicates weight. Flash communicates success/failure moments. These are state-change signals with proportional intensity: major impacts → full shake; minor hits → flash only. Misuse (constant shake, frequent flash) produces noise that masks genuine critical signals. ✅ (GDC game-feel talks; CHI 2019 juiciness study: proportionality is a design constraint, not aesthetic preference.)

- GDC game-feel talks: *"camera shake is used to communicate significant events — explosions, taking damage, high-impact actions."* ✅ Betterlink blog; FIERY THINGS dev blog; GameDev Academy.
- **Test for:** Do playtesters report the game "feels good to play," or do they describe hitting enemies as "hitting air"? Game feel is a testable perception.
- **Failure mode — "Shake abuse":** camera shake on every bullet impact, every jump, every footstep. The signal degrades to visual noise; players disable screen shake as quality-of-life. A shake that was meaningful has become irrelevant.

## C.3 Signpost affordances through the environment before text

Before the player must perform an action, they must encounter a demonstration or an environmental signifier of it. Glowing edges on climbable surfaces, worn paths toward important locations, contrast-highlighted interactables — all convey affordances without UI text. When the environment cannot carry the signal, spatial UI (prompt above object) is the fallback, not the first resort. ✅ (Norman 2013, affordances/signifiers; *Dark Souls* / *Ghost of Tsushima* exemplars; *Portal*.)

- *Dark Souls* (FromSoftware, 2011): glowing bonfires as save-point signifiers — no tooltip, no tutorial text; the glow *is* the affordance signal. ✅ AmrSalehDuat.com; Medium affordances-in-level-design.
- *Ghost of Tsushima* (Sucker Punch, 2020): guiding wind direction = objective; golden birds = fox shrines; rising smoke columns = nearby quests — zero non-diegetic markers because the world carries all navigation signals. ✅ Andy's Cabin Games 2021; developer interview.
- **Test for:** Can a playtester who has skipped all tooltips still discover the first 3 core mechanics through environmental play?
- **Failure mode — "Wall of text tutorial":** the first 5 minutes is reading static instruction screens. Players skip these; then they can't play. Tutorial text is not a signifier — it is what you produce when signifiers failed.

## C.4 Give success and failure distinct signal vocabularies across all channels

Success and failure must use clearly different visual, audio, and haptic channels. Players form mental models from repeated signal-consequence pairings; conflated signals permanently confuse state understanding. ✅ (Hodent 2017: red = failure/damage/danger — reserve it; color-psychology PvP experiments.)

- Hodent: PvP teams using red for *both* team identification *and* damage indicators cause persistent confusion; "blue team gains statistical advantage over red team" in experiments where red primes failure. ✅ Celiahodent.com color psychology.
- **Test for:** Eyes closed — can a player correctly distinguish "I succeeded" from "I failed" from audio alone?
- **Failure mode — "Ambiguous ding":** a single pleasant chime for level completion, item pickup, achievement, *and* errors. Players stop trusting signals because they no longer map reliably to state.

**→ Procedural / headless implication.** Procedurally generated content risks "signifier deserts" — a generated room may accidentally eliminate all environmental cues for a mechanic (a dark corridor with no light-based affordance, a room where the climbable surface looks identical to the background). Mitigation: **cross-reference mechanic requirements against the environmental signal budget at generation time.** Every mechanic the player must execute in a generated room should have at least one guaranteed environmental signifier injected by the generation rules — not left to chance. Screen-space effects (meta UI, hit-stop, shake) survive generation; diegetic signifiers (glowing specific authored geometry) and environmental storytelling (worn paths) do not.

---

# Sub-domain D — Menu & Navigation Flow

> Navigation depth is measurable friction. Every press/tap is a cost; every layer of menu adds to the player's extraneous load. The floor is ≤2 inputs from any game state to any primary action. ✅ (TLOU weapon-swap redesign; CHI 2019; Kotaku Naughty Dog interview.)

## D.1 Primary actions must reach ≤2 inputs from any game state

Define "primary action" as anything taken more than once per minute during play. No primary action may require more than two button presses/clicks to reach from any in-game state, including from within a submenu. Count every tap/press. ✅ (Kotaku, Naughty Dog interview; multiple game UX guides.)

- *The Last of Us*: weapon swap redesigned from menu-access (too slow) to D-pad left/right (one input) after combat frustration was observed in playtests. ✅ Kotaku 2014.
- Riot/Valorant: *"reducing menu layers in early UX tests increased new-player retention by ~20%."* ⚠️ (Single secondary source — treat as directional, not empirical benchmark.)
- **Test for:** Map every primary action to its input path from the default game state. Any path ≥3 inputs is a red flag.
- **Failure mode — "Menu hell":** open inventory → scroll to item type → select item → confirm equip → close inventory — five inputs for a combat action. Produces analysis paralysis and combat frustration.

## D.2 Design for the primary input modality first; validate every secondary modality explicitly

Identify the primary input modality (controller / KBM / touch). Build and test menus for that modality first. Then explicitly port and validate each secondary modality. A menu that works with mouse but not gamepad (no focus state, no D-pad flow) is broken for ~40% of PC players and 100% of console players. Focus state (visual highlight on current selection) is a requirement. ✅ (UE5 gamepad nav guide; Bugnet blog; mobile thumb-zone research.)

- Gamepad focus navigation requires `SetFocus()` called explicitly on a widget; without it, D-pad inputs are consumed by the game's action layer and never reach the menu system. ✅ UE5 gamepad nav guide.
- Mobile: primary actions in the bottom 60–70% of the screen (comfortable thumb reach); destructive / secondary actions in stretch zones to add intentional friction. ✅ Parachute Design; UX Movement.
- Fitts's Law minimum touch target: 9.2mm physical (≈44px at 96dpi); below 7mm error rates spike steeply. ✅ Smashing Magazine 2012; NNG 44×44 CSS px recommendation.
- **Test for:** Navigate the entire menu with D-pad only. Can you reach every option? Is the current selection always clearly indicated?
- **Failure mode — "Gamepad ghost":** menus appear navigable by controller; D-pad does nothing; players on couch are forced to mouse. Focus state is missing; gamepad testing was never run.

## D.3 Settings and options screens are first-class features; ship infrastructure mid-production, not at polish

Settings (graphics, audio, controls, accessibility) must be accessible from the main menu before first gameplay and from the pause menu during play, and must persist across sessions. They are not a polish pass — they are infrastructure for every player who cannot use the default configuration. ✅ (Naughty Dog blog 2020; PlayStation Blog 2021, *Returnal*.)

- *The Last of Us Part II*: 60+ accessibility settings, three presets, each option independently tunable — all accessible before first gameplay. ✅ Naughty Dog official blog 2020.
- *Returnal*: accessibility integrated into core settings rather than a separate submenu — the team "intentionally avoided UI segregation." ✅ PlayStation Blog 2021.
- **Test for:** Can a player with motor impairment (one-handed) configure the game to be playable before touching any gameplay state?
- **Failure mode — "Accessibility afterthought":** settings screen ships in the last two weeks, untested, with options that conflict (remapped controls that break gameplay systems; subtitle size changes that overflow UI containers).

## D.4 Match navigation convention to platform/genre; justify any deviation

Players have established mental models: B/Circle = back, A/Cross = confirm on console; Escape = close on PC; swipe = navigate on mobile. Violating these conventions produces consistent, measurable confusion. Novel paradigms (radial menus, gestures, voice) justify their cost only when they deliver a significant interaction improvement over convention. ✅ (Multiple game UX guides; Salama 2023.)

- *Breath of the Wild*: radial weapon-select that *pauses gameplay* — justified because weapon-switching frequency and the game's tactical pace make the interruption acceptable. ✅ Multiple HUD analyses.
- *Monster Hunter* radial item wheel: fits the game's action context; steep learning curve when the item set is novel to new players. ✅ Salama "Minimal HUD Paradox."
- **Test for:** Without instruction, can a new player return to the previous menu screen on each supported input modality?
- **Failure mode — "Clever but alien":** a novel navigation paradigm that feels elegant to the designer is a roadblock to every player encountering it without instruction.

**→ Procedural / headless implication.** Menus are almost never procedurally generated — they are static authored systems. However, menu *content* can be data-driven: a game where skills or items are generated must produce UI for them without pre-authored icons or labels. Design menu systems as *templates* that receive any content without special-casing. Use text labels as fallback for any generated element without a defined icon. Ensure icon templates have a visually distinct "blank/unknown" state. Reflowable text containers must be tested at maximum generated label length (a 16-character authored name may become a 48-character generated name).

---

# Sub-domain E — Accessibility-Adjacent UX

> Accessibility is not polish — it is product quality and it is required. *The Last of Us Part II* (60+ options) is the industry benchmark. Every color-only signal, every unmappable control, every hardcoded screen flash is a playable-experience lockout for a real population of players. ✅ (Naughty Dog 2020; WCAG 2.1; Xbox Accessibility Guideline 112.)

## E.1 Every color signal must have a redundant shape, icon, or text channel

~8% of males and ~0.5% of females have color vision deficiency. Any information conveyed by color alone is inaccessible to this population. Redundancy is mandatory: shape, icon, pattern, text, or position. "Red = low health" fails. "Red *blinking heart icon* = low health" passes. ✅ (WCAG 1.4.1; *Returnal* colorblind palettes; game accessibility best practices.)

- *Returnal*: three colorblind palettes (deuteranopia, protanopia, tritanopia) with adjustable intensity sliders; collectible beam colors recolored in all three modes. ✅ PlayStation Blog 2021.
- Minimap: red = enemy dot, green = ally dot — colorblind lockout. Fix: circle = enemy, triangle = ally (shape redundancy). ✅ Colorblind accessibility guides.
- **Test for:** Simulate deuteranopia using a filter (iOS accessibility setting, browser extension). Does all game state remain legible?
- **Failure mode — "Colorblind lockout":** ally/enemy distinction is color-only; a colorblind player cannot play the designed experience.

## E.2 Text must be legible at the minimum intended viewing distance; provide a size slider

Body text: minimum 4.5:1 contrast against background (WCAG floor, not target). Large text/UI components: 3:1. For TV play at 3m, font size minimums increase significantly. Always provide a text size slider with at least three steps. Test legibility against the *brightest* and *darkest* scenes in the game — dynamic backgrounds reduce effective contrast dramatically. ✅ (WCAG 2.1; Naughty Dog 2020; game accessibility resources.)

- *The Last of Us Part II*: subtitle size, color, background opacity, speaker name display, off-screen speaker directional arrow — all independently configurable. ✅ Naughty Dog 2020.
- **Test for:** Can a player read all UI text from standard play distance on the smallest tested display without adjusting settings?
- **Failure mode:** "UI text at 18px on a 4K display at 3m" — technically valid CSS, effectively unreadable on a TV. Common in PC-first games ported to console.

## E.3 Ship full control remapping with per-action hold/toggle customization

Full button remapping for every input modality (gamepad, KBM). Allow hold-vs-toggle and press-duration configuration per action for players with reduced hand strength. Preserve a "restore defaults" path. Validate that remapped configurations cannot produce conflict states (two actions on the same input). ✅ (Naughty Dog 2020; Xbox Accessibility Guideline 112, Microsoft Game Dev.)

- *The Last of Us Part II*: per-action hold/toggle for melee combos, aiming, sprinting, crafting, bow firing, hold-breath, listen mode — each independently configured. ✅ Naughty Dog blog; GameAccess SpecialEffect analysis.
- Xbox Accessibility Guideline 112: full remapping recommended across all first-party Xbox titles. ✅ Microsoft Learn/GameDev.
- **Test for:** Can a single-handed player (right hand only) configure a playable control scheme?
- **Failure mode — "Partial remapping":** face buttons remappable but triggers and D-pad not; or remapping resets on every session; or two actions mapped to the same input silently break both.

## E.4 Avoid photosensitivity triggers; provide opt-out for every vestibular effect

Flash frequency >3Hz over a large screen area can trigger seizures (Harding Flash and Pattern Analysis standard). Camera roll, rapid FOV changes, and continuous screen shake can trigger vestibular disorders (~35% of adults over 40). Every meta UI effect (screen flash, camera shake) must be independently togglable. ✅ (Harding standard; TLOU Part II; multiple accessibility guidelines.)

- *The Last of Us Part II*: high-contrast mode, screen flash controls, each individually configurable. ✅ Naughty Dog 2020.
- **Test for:** Does disabling screen shake in settings disable *all* instances, including hardcoded boss abilities?
- **Failure mode — "Settings that don't work":** player disables screen shake, 90% disappears, one boss ability still fires a hardcoded shake that was not routed through the setting. One uncontrolled flash for a photosensitive player is one too many.

**→ Procedural / headless implication.** Procedurally generated content risks generating accessibility violations at scale: random palette generation may produce contrast ratios below 4.5:1; generated particle effects may inadvertently exceed 3Hz flash frequency; procedural audio may spike volume above safe levels. Mitigation: **run accessibility validators as generation-time constraints, not post-hoc reviews.** For color: clamp generated palettes to a pre-validated safe set. For particles: rate-limit screen-space flash events at the renderer level, independent of content. For audio: normalize all generated audio against a peak limiter.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Diegetic theater | In-world UI commits ship with emergency overlay added; two competing systems | Non-diegetic default; reserve diegetic for elements that gain dramatic weight in-world (A.1) |
| Narrative incoherence | Spatial UI the fiction can't explain (floating arrow in photorealistic world) | If fiction can't explain it, use non-diegetic overlay (A.2) |
| Obscured geometry | Full-screen meta flash at exact moment player needs to dodge | Scale meta effects proportionally; budget vestibular impact (A.3) |
| Information anxiety | Player pauses every 30s to check state invisible during play | Tier-1 data permanent at periphery; never in a menu (B.1) |
| Tutorial front-loading | Static instruction screen for 5+ minutes before first input | Progressive disclosure; teach inline with first encounter (B.2) |
| Icon soup | Toolbar of abstract symbols; hover required to understand each | ≥80% intent-recognition threshold; redesign, don't label (B.3) |
| Uniform grey soup | All HUD elements equal contrast and size | Visual weight hierarchy: primary dominates, secondary recedes (B.4) |
| Silent hit syndrome | Action connects; no audio, no flash, no camera response | Immediate visual + audio synced ≤15ms on every state change (C.1) |
| Shake abuse | Camera shake on every bullet/footstep/jump | Proportional shake scaled to magnitude; reserve max for rare events (C.2) |
| Wall of text tutorial | First 5 minutes is reading instructions | Environmental signifiers first; text tutorial = signifier failure (C.3) |
| Ambiguous ding | Same chime for success, failure, pickup, and error | Distinct signal vocabulary per outcome, testable audio-only (C.4) |
| Menu hell | 5 inputs to equip a frequently-used item | Primary actions ≤2 inputs from any game state (D.1) |
| Gamepad ghost | Menu navigable by appearance but D-pad does nothing | Explicit focus state; test D-pad-only on every menu (D.2) |
| Accessibility afterthought | Settings screen ships in last two weeks untested | Settings infrastructure mid-production; treat as game system (D.3) |
| Colorblind lockout | Ally/enemy distinction color-only | Redundant shape or icon on every color signal (E.1) |
| TV text | 18px text on 4K display at 3m distance | Text size slider + contrast against brightest/darkest scene (E.2) |
| Settings that don't work | Screen shake off but boss ability still shakes | Route every instance through the setting; validate all (E.4) |

---

## Caveats

- **Fagerholt & Lorentzon's taxonomy was developed for FPS games.** The framework extrapolates well but was not empirically validated across strategy, puzzle, or narrative games. Apply with genre-aware judgment.
- **Diegetic UI cost estimates are practitioner heuristics, not published data.** "3–5x production cost vs. non-diegetic" reflects multi-discipline coordination at AAA scale. At solo/small-team scale the ratio may differ.
- **The Riot/Valorant retention figure (~20% uplift from reduced menu layers) is from a single secondary source.** ⚠️ Treat as directional evidence, not an empirical benchmark.
- **Accessibility standards update.** WCAG updates; platform certification requirements (Xbox, PlayStation) add constraints beyond WCAG. Principles here are durable; verify specific numerical thresholds against current certification requirements before ship.
- **Juiciness research was conducted on abstract prototypes.** The CHI 2019 study used simplified game environments. Transfer to complex genre-specific contexts is directionally valid but not 1:1. Playtesting remains the ground truth.
- **Generated HUD specs require human playtest validation.** A mechanically correct generated spec can still produce player confusion that only emerges in real play. Flag any HUD element inferred rather than playtested as ⚠️ unverified until a session confirms it.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
