# U1 — Level Design: Craft, Grammar, and Process

> **Audience:** AI game-designer agent (headless, cross-genre, may author content procedurally).
> **Scope:** Genre-agnostic principles. Genre-specific carve-outs are labeled explicitly.
> **Verification key:** ✅ verified (≥2 independent/primary sources) · ⚠️ sourced-but-unverified (single source or community consensus) · ❌ refuted
> **Sources used:** GDC talks (Scott Rogers 2009, David Shaver 2018, Valve 2006), Gamedeveloper.com postmortems, The Level Design Book (leveldesignbook.com), Pete Ellis WOLD tutorials, Mark Brown / Game Maker's Toolkit Boss Keys, FromSoftware analysis (James Roha / Medium, illusorywall), Christopher Totten's *An Architectural Approach to Level Design* (2nd ed.), Antonios Liapis's PCG Book (dungeons chapter), academic PCG research (van der Linden et al., Smith et al. LaunchPad), CS:GO chokepoint analysis (WOLD).

---

## 1. TL;DR — Five Highest-Leverage Principles

- **Guide before you challenge.** Players must always know where to go, even if they don't know what they'll find there. Wayfinding (landmarks, light, leading lines) is not decoration—it is structural. Confusion about navigation is the single most common cause of play-test failure.
- **Teach through space, not text.** The antepiece pattern (safe introduction → full test) is the most-proven teaching tool in level design, used consistently from *Super Mario Bros.* to *Portal* to *Dark Souls*. Explicit tutorials fail players who skip them; implicit antepieces cannot be skipped.
- **Pacing is a rhythm of peaks and valleys.** Every intensity peak requires a corresponding rest beat. Sustained high intensity produces fatigue, not excitement. The L4D AI Director codifies this as a machine; hand-designed levels must encode it as spatial sequencing.
- **Interconnection beats hub-and-spoke.** Shortcuts that loop back to known space (shortcuts that "collapse" the mental map) produce the most powerful discovery moments in games. Dark Souls 1 is the canonical exemplar; the technique is applicable at any genre and scale.
- **Blockmesh first, always.** Committing to art before validating layout is the most expensive mistake in level design. Expect to rebuild after the first playtest; blockouts make that free.

---

## 2. Key Findings

1. **Wayfinding is multi-redundant by default.** Left 4 Dead's designers state paths are "implied by light placement" as the primary channel, with geometry, color contrast, NPC movement, and sound as backups. Single-channel wayfinding fails players with accessibility needs and fails procedural layouts that can't guarantee one channel is visible. ✅ (Shaver 2018; LevelDesignBook wayfinding chapter)

2. **The critical path is a design communication tool, not a player constraint.** It visualizes the beat map for collaborators and scopes work. Goldeneye 007's Martin Hollis notes their "sloppy unplanned approach" produced more believable, multi-route spaces precisely because they avoided over-engineering the golden path. ✅ (LevelDesignBook criticalpath; Hollis postmortem)

3. **Dark Souls 1's world is built from explicit loop graphs.** From Softworks' internal cross-sectional sketches verified that areas sit in vertical relationship (Blighttown under Firelink Shrine, Anor Londo above Sen's Fortress), and every main path is "forcibly made into a circle." ✅ (illusorywall Tumblr analysis; James Roha Medium; ResetEra DS1 thread)

4. **Valve's Cabal process produced the antepiece pattern independently of Nintendo.** For Half-Life, the Cabal determined "when and how every monster, weapon, and NPC was to be introduced" and ran 200+ playtests. The content-density principle—"the amount of things that happen to the player per unit of distance"—directly preceded the spatial teaching methods Portal later perfected. ✅ (Gamedeveloper.com Cabal article; GDC 2006 HL2 design process PDF)

5. **Portal's test chambers follow a strict three-phase grammar: introduce mechanic in isolation → test application → combine with known mechanics.** The "flinging" mechanic required more iteration than almost any other chamber because spatial understanding precedes intentional execution. ✅ (Valve test chamber design guide; Portal wiki design notes)

6. **Intensity curves with named phases are measurable and encodable.** Left 4 Dead's AI Director formalizes pacing as Build → Peak → Relax states, with Relax lasting 30–45 seconds, truncated when survivors move again. This is the machine-readable form of the "inhale/exhale" principle every level designer uses informally. ✅ (L4D wiki Director entry; Valve Mike Booth GDC paper reference)

7. **Lock-and-key structure is best represented as a mission graph, not a floor plan.** Mark Brown's Boss Keys diagrams reveal that many seemingly complex Zelda dungeons offer only one viable path; the graph exposes false choice. Good lock-and-key design produces meaningful branching at graph nodes. ✅ (GMTK Boss Keys series; boristhebrave.com lock-and-key analysis)

8. **Competitive FPS maps converge on three lanes, two routes per objective.** Analysis of Dust II, Temple of Anubis (Overwatch), and CoD Sovereign shows this independently. Four lanes make objective defense impossible; two lanes remove meaningful attacker choice. ✅ (Gamedeveloper.com FPS layout analysis; WOLD CS:GO chokepoint guide)

9. **Blockmesh metrics are engine-specific but player-body-relative constants.** Player bounding box and eye height are the anchors; all cover heights, hallway widths, and jump distances derive from them. TF2's combat ranges (close: ≤256 units, medium: ≤1024 units) are the most cited worked example. ✅ (LevelDesignBook metrics table; TF2 Valve design docs)

10. **PCG level grammars that encode gameplay beats—not geometry—produce the most legible procedural outputs.** Smith et al.'s LaunchPad system generates Mario-style levels from "rhythm groups" of desired player actions. Geometry is derived from beats, not the reverse. ✅ (LaunchPad academic paper; Liapis PCG Book dungeon chapter)

---

## 3. Details

---

### 3.1 The Language of Guiding the Player

#### 3.1.1 Critical / Golden Path

**Rule:** Define the critical path before building anything else; annotate it with numbered beats and draw player movement arrows over your layout sketch.

**Exemplar:** Valve's Cabal process (Half-Life, 1998) established that the team needed to know "what skills we expected the player to have, and how we were going to teach them those skills" at each stage before individual designers built their segments. ✅ (Gamedeveloper.com Cabal article)

**Test for:** Starting from the player spawn, can you walk to the exit using only afforded routes (no clipping, no designer knowledge) in under 30 seconds of continuous movement? If not, the critical path is broken or invisible.

**Failure mode:** Designers optimize for visual impressiveness along non-critical routes, starving the main path of wayfinding resources. Players explore impressively lit dead-ends and miss the exit.

**Procedural note:** Generate critical path first as a graph (start → beat₁ → beat₂ → … → exit), then instantiate geometry around each node. Never start with room geometry and try to retrofit a path.

---

#### 3.1.2 Breadcrumbing

**Rule:** Place collectibles, environmental details, or NPC behaviors along the intended route in a line of sight chain—each crumb visible from the previous one.

**Exemplar:** Donkey Kong Country places banana trails that visually connect through spaces. Half-Life 2 uses NPCs who "head to the left" from the opening train car; lighting reinforces this with the left wall brighter than the right. ✅ (LevelDesignBook wayfinding; intermittentmechanism.blog HL2 analysis)

**Test for:** Can a first-time player identify the next waypoint within 3 seconds of entering any new space, using only environmental cues?

**Failure mode:** Breadcrumbs placed at designer navigation speed (fast) exceed the player's visual scanning rate and cluster behind the player's field of view.

**Procedural note:** Encode breadcrumb positions as waypoints on the critical path graph. The generator must guarantee each waypoint has line-of-sight from the previous one at player eye height (not top-down).

---

#### 3.1.3 Leading Lines and Sightlines

**Rule:** Construct architectural features (roads, train tracks, fences, fallen logs, lighting rakes) that point toward the next waypoint and can be perceived at a glance.

**Exemplar:** Team Fortress 2 uses train tracks through maps to create visual flow; Half-Life 2 uses a doorframe that "points outward into the city square, accompanied by birds flying upward" to highlight The Citadel as the direction-of-travel landmark. ✅ (LevelDesignBook wayfinding; Shaver GDC 2018)

**Test for:** Screenshot the level at player eye height. Can you draw a line from any ground-plane position to the next beat using only the rendered geometry? If lines don't converge on a destination, leading lines are absent.

**Failure mode:** "Leading lines" are added in art pass after layout is locked, forcing lines that terminate on walls instead of goals.

**Procedural note:** Sightlines are only checkable in 3D. A 2D PCG system cannot guarantee them; if generating for 3D, add a sightline-validation pass that traces rays from player-eye positions at each beat to the next.

---

#### 3.1.4 Landmarks and Weenies

**Rule:** Place at least one large, visually distinctive landmark with near-constant line of sight that serves as the overarching goal of the level or zone. This is what Walt Disney called a "weenie"—used precisely because it pulls players toward it.

**Exemplar:** Scott Rogers's GDC 2009 "Everything I Learned About Level Design I Learned from Disneyland" codified this: each park zone is organized around a central visual anchor (Sleeping Beauty Castle, the Matterhorn). Half-Life 2's Citadel tower performs the same function—visible from most of the city chapters, it always tells Gordon which direction matters. ✅ (GDC Vault 2009 Rogers; LevelDesignBook Disneyland study; intermittentmechanism HL2)

**Test for:** From any navigable position in the level, is there at least one large structural landmark visible that unambiguously indicates the direction of progress?

**Failure mode:** Weenies are blocked by procedural geometry accumulation or fog. The landmark becomes invisible in exactly the areas where players most need orientation.

**Procedural note:** Reserve a non-occluded height or silhouette slot for the weenie. For top-down or isometric games, the weenie is often a distinctive biome or building cluster, not necessarily a tall object.

---

#### 3.1.5 Light, Color, and Contrast as Signpost

**Rule:** Light the exit, door, interactive object, or next area more brightly (or with more saturated/contrasting hue) than its surroundings. Darkness signals danger or "not yet." Brightness signals safety and direction.

**Exemplar:** David Shaver's GDC 2018 "Invisible Intuition" (Naughty Dog/Respawn) states players "focus on contrast in color, shape, lighting, and movement" and don't look upward unless something draws their eye. Left 4 Dead's safe rooms are visually distinct warm-lit spaces in otherwise cold, hostile environments. ✅ (Shaver GDC 2018 director's cut PDF; LevelDesignBook wayfinding)

**Test for:** Desaturate a screenshot. Are interactive/critical elements still distinguishable from background by value alone? If not, the signpost relies on hue only (fails colorblind players and poor-lighting conditions).

**Failure mode (refuted):** "Players always look toward the brightest spot." ❌ This is false if enemies, fire, or distracting VFX are also bright. Light works as signpost only when it's contrastive in context—a bright area in a dark level, not just the brightest area of a uniformly lit level.

**Procedural note:** Assign a "signpost" lighting layer separate from ambient/mood lighting. The generator should place signpost lights at every critical waypoint entrance before any other light pass.

---

#### 3.1.6 Affordances and Signifiers

**Rule:** Every interactable object must communicate what it does before the player commits to an action. Affordance (what's possible) is built into shape and physics; signifier (how to notice it) is added via color, animation, and sound.

**Exemplar:** Uncharted's planks extending off ledges make the intended grab-point obvious through shape alone. Portal's colored gel trails and button pedestals visually encode their function before the player reads a single text prompt. ✅ (LevelDesignBook composition; multiple level design theory sources)

**Test for:** Cover the UI. Does the player attempt the intended interaction within 10 seconds of seeing the affordance for the first time, based solely on environmental information?

**Failure mode:** Designers use arbitrary objects that look like decoration as key interactables (e.g., a generic crate is the puzzle switch). Players walk past.

**Procedural note:** Maintain a vocabulary list of affordance-signifier pairs. Generated content should only instantiate interactables from the approved vocabulary; novel interactables require a hand-authored introduction.

---

### 3.2 Structure

#### 3.2.1 Linear, Nonlinear, Hub-and-Spoke, Open

**Rule:** Choose structural type first, before layout. Each type has a different pacing contract with the player.

| Structure | Pacing control | Player agency | Design cost | Canonical example |
|-----------|---------------|---------------|-------------|-------------------|
| **Linear** | Maximum—designer controls beat timing | Minimal | Lowest | Half-Life, Call of Duty campaigns |
| **Multi-path linear** | High—branches converge at beats | Moderate | Medium | Halo CE, Dishonored levels |
| **Hub-and-spoke** | Medium—player chooses spoke order | High within spokes | High | Dark Souls (Firelink), Zelda dungeons |
| **Open world** | Low—player may skip beats | Very high | Highest | Skyrim, Red Dead Redemption 2 |

✅ (LevelDesignBook criticalpath; iuliu-cosmin-oniscu.medium.com linear/multipath/open analysis)

**Test for:** Can you draw the level's structure as one of these four types in under 60 seconds? If not, the structure is ambiguous and will produce inconsistent pacing.

**Failure mode:** Designers attempt hub-and-spoke without budgeting for the art/content cost of all spokes being authored to equal quality. Players notice the weak spoke immediately.

**Procedural note:** Open world and hub-and-spoke structures require the generator to understand the full graph before placing content. Generating spoke by spoke without global context produces unbalanced worlds.

---

#### 3.2.2 Lock-and-Key and Gating

**Rule:** Model your level's progression as a mission graph before placing any geometry. Each node is an area; each edge is a traversal; each lock is a conditional edge that requires a key to unlock.

**Exemplar:** Mark Brown's Boss Keys series graphs Zelda dungeons and reveals that the original Zelda's dungeons are dense, branching graphs—players can pursue multiple keys simultaneously. Later games (Phantom Hourglass) collapse to near-linear chains. The graph makes this visible instantly. ✅ (GMTK Boss Keys YouTube; boristhebrave.com lock-and-key)

**Test for:** Convert your level to a mission graph (nodes = rooms/areas, edges = connections, diamonds = locks). Count the branching factor at each node. Does the player ever have ≥2 meaningful choices? If every node has branching factor 1, the lock-and-key structure is decorative, not structural.

**Failure mode (anti-pattern):** "Fake complexity"—a maze-like floor plan that resolves to a single valid path when graphed. Players feel trapped and cheated when they realize there was only one option.

**Procedural note:** Generate the mission graph first (preferably with a minimum branching-factor constraint), then use it to drive geometry generation. Tools: Zelda-style graph grammars (van der Linden et al. 2013). For procedural roguelikes, the Spelunky approach generates a guaranteed critical path first as a sequence of linked room slots, then fills non-critical slots with optional content.

---

#### 3.2.3 Interconnection and Shortcuts

**Rule:** Design at least one shortcut per major zone that collapses an earlier traversal into a few seconds. Announce the shortcut's existence from the destination side (players see the locked door before they find the key).

**Exemplar:** Dark Souls 1 is the canonical case. Undead Burg → Parish elevator returns to Firelink Shrine, collapsing a 15-minute traversal into 10 seconds. The design principle: "a path that could be reached in a straight line is forcibly made into a circle." The player's mental map expands when the shortcut opens—a signature moment described by players as "amazement at how the world fits together." ✅ (illusorywall Tumblr DS1 analysis; thegamer.com DS1 level design; James Roha Medium; quora.com DS shortcuts discussion)

**Shortcut placement rules** (verified from DS1 analysis):
1. The shortcut must open from one end only on first approach (maintaining cost on initial traversal).
2. The shortcut must link two areas the player has already seen, not introduce new space.
3. The shortcut's opening must be narratively legible (a gate, a lever, a ladder—not a magic portal).

**Test for:** After completing the zone normally, can a player traverse it again in ≤20% of the original time using available shortcuts?

**Failure mode:** Shortcuts are placed at the designer's preference for "cool moments" rather than at high-friction traversal points. Players don't need shortcuts where movement is already fast.

**Procedural note:** Encode shortcuts as "bonus edges" in the mission graph that activate after certain flag conditions. The generator should guarantee at least one bonus edge per major hub-to-spoke traversal.

---

#### 3.2.4 The Metroidvania Loop

**Rule:** Every new ability must enable at least three uses: (1) progress past the current gate, (2) shortcut through earlier space, (3) reframe combat or puzzle space. If an ability enables only use #1, it is a key, not an upgrade.

**Exemplar:** Super Metroid's Grapple Beam: (1) crosses specific lava pits in Norfair, (2) unlocks shortcut through Brinstar ceiling section, (3) changes enemy combat options. Hollow Knight's Crystal Heart: (1) crosses Crystallized Mound gap, (2) provides fast travel through Kingdom's Edge, (3) dashes through enemy projectiles. ✅ (Dreamnoid Metroidvania guide; Diva Portal Metroidvania thesis)

**Test for:** For each new ability, list three distinct uses across three different areas before locking the ability behind a boss gate. If you can't find all three, the ability is under-designed.

**Failure mode:** Ability bloat—later abilities supersede earlier ones with no backtrack payoff. Players forget earlier areas exist and experience the map as linear even though it's connected.

**Procedural note:** The generator must maintain an "ability dependency graph"—areas are tagged with which abilities unlock them. New ability events must trigger a re-evaluation of accessible areas to surface previously gated content.

---

### 3.3 Teaching Through Space

#### 3.3.1 The Antepiece (Safe Introduction)

**Rule:** Before a mechanic is tested in a high-stakes context, introduce it in a consequence-free environment where failure has zero cost and no other variables are active.

**Exemplar 1:** Super Mario Galaxy 2 (Cakewalk Flip). First encounter with flipping platforms is placed above solid ground. The second encounter is above a chasm. Players who didn't learn the timing in the first encounter die in the second—but they were given the chance. ✅ (TVTropes Antepiece; Nintendo level design analysis multiple sources)

**Exemplar 2:** Zelda: Skyward Sword. First Timeshift Stone is in a hazard-free clearing. All subsequent stones are adjacent to quicksand or spiky obstacles that the player already understands because of the antepiece. ✅ (TVTropes Antepiece)

**Exemplar 3:** Portal's first chambers. Buttons and cubes are introduced in rooms where there is no wrong way to use them before they become load-bearing puzzle elements. ✅ (Portal test chamber design guide; portalwiki.net)

**Test for:** Does every mechanic in the game have an antepiece that precedes its first lethal encounter? If a player encounters a mechanic for the first time in a fail state, the antepiece is missing.

**Failure mode:** Tutorial rooms that are visually separate from the game world and break immersion. The antepiece should be embedded in normal-looking level space, not a "training ground" aesthetic.

**Procedural note:** The generator must know which mechanic each generated room features and must guarantee a mechanic appears in a consequence-free context before a lethal context. This is a sequencing constraint, not a content constraint.

---

#### 3.3.2 Introduce → Develop → Twist → Conclude

**Rule:** Within a single level or zone, cycle a mechanic through four phases: first encounter (safe), escalation (adds complexity/variables), unexpected application (twist that uses the mechanic in a new way), and resolution (player demonstrates mastery under full load).

**Source:** Nintendo's level design methodology, grounded in the Japanese narrative structure *kishoutenketsu* (intro, develop, change, reconcile). ✅ (GDD Super Mario World analysis; Gamedeveloper.com "secret to Mario level design")

**Exemplar:** Super Mario World's Yoshi levels. First screen: Yoshi eats a shell (introduce). Later: Yoshi must eat a shell to break a barrier (develop). Mid-level: Yoshi must spit a bouncing shell at a moving target (twist). Boss: Shell-riding, targeting, and platform jumping combined (conclude).

**Test for:** Map each room or section in the level to one of the four phases. Do all four appear? Does the order respect the sequence? Can a player who failed the "twist" retry it from a checkpoint without replaying "introduce" and "develop" again?

**Failure mode:** The "twist" is placed at the final gate before the exit, meaning players who fail it must replay the entire level. This punishes exploration of the mechanic and discourages experimentation.

**Procedural note:** The generator's "beat sequence" for a level should include flags for all four phases. A level generated with only Introduce and Develop (no Twist) will feel repetitive; a level starting with Twist (no Introduce) will feel unfair.

---

#### 3.3.3 The "Gym" — Isolated Mechanical Space

**Rule:** For games with rich combat systems, provide a low-stakes zone near the beginning where multiple enemy types can be encountered individually before combination encounters appear.

**Exemplar:** Half-Life's Lambda Core section before the alien world introduces enemy types one by one as Gordon progresses, with space between encounters to assess each threat. Halo CE's first encounters with Grunts and Elites are in large, forgiving outdoor spaces before corridor ambushes begin. ✅ (Gamedeveloper.com Halo CE encounter design article; Valve Cabal article)

**Test for:** Can a player encounter and defeat each enemy type in the game in an isolated single-type encounter before facing mixed enemy groups?

**Failure mode:** Tutorial areas that are too safe—never teach the actual danger of the enemy. Players who "complete the gym" are surprised by lethality in actual combat.

---

### 3.4 Encounter and Arena Design

#### 3.4.1 Arena as a Sentence

**Rule:** Each combat arena should communicate a single tactical proposition—one or two meaningful choices for the player, not a neutral empty box. The geometry is the argument; the player's movement through it is the resolution.

**Exemplar:** DOOM 2016 arenas are circular or figure-eight layouts with multiple height tiers, designed so that standing still produces death (enemies pressure from all vectors) while constant movement cycles through cover nodes. Each arena's geometry enforces the "push forward" combat loop rather than stating it in a tutorial. ✅ (Gamedeveloper.com DOOM 2016 analysis; ResetEra DOOM 2016 level design thread)

**Test for:** Can you describe the arena's tactical proposition in one sentence? ("This arena punishes camping by placing the only ammo spawns at the far end." "This arena rewards high ground by placing the sniper rifle at elevation.") If you can't articulate it, the arena has no proposition.

**Failure mode:** Arenas designed for visual spectacle (impressive architecture) that undermine the tactical proposition—e.g., a gorgeous coliseum with uniform flat ground and no cover variation.

---

#### 3.4.2 Cover

**Rule:** Provide both half-height cover (enables firing over) and full-height cover (enables concealment) in every arena. Half-height cover should be more common; full-height cover enables flanking routes.

**Exemplar:** Halo CE's outdoor combat sections use boulder and hill terrain as half-height cover with full-height ridgelines for flanking. The FEAR games are cited in Fullbright's encounter design analysis as best-practice for varied, clustered cover layout. ✅ (fullbrightdesign.com FEAR analysis; Halo level design multiple sources)

**Metric (TF2 reference):** Cover height should match player crouch height (Quake/HL: 36 units crouched; Unreal: ~88 cm). Cover taller than standing player height is "full cover." ✅ (LevelDesignBook metrics table)

**Test for:** Draw a top-down silhouette of the arena. Are there at least 3 distinct cover positions that offer different lines of fire toward the enemy's likely starting positions? Can a flanking player traverse the arena without entering any enemy's sightline if they time correctly?

**Failure mode:** "Cover theater"—cover placed to look good in screenshots but positioned perpendicular to enemy fire rather than providing actual protection.

---

#### 3.4.3 Chokepoints

**Rule:** Place chokepoints between arenas (not inside them) to create transition moments. Chokepoints define where contested space begins. The defending team should be able to reach the chokepoint ~5–12 seconds before attackers. ✅ (WOLD 6 Principles CS:GO Chokepoints; Gamedeveloper.com FPS layout)

**Types:**
- **Hard chokepoints:** Single narrow passage; both teams must commit (Inferno's Banana, HL2's server room doors).
- **Soft chokepoints:** 2–3 tight paths that a skilled defender can monitor simultaneously.

**Test for:** Stand at the chokepoint and count viable entry vectors from the attacker side. More than 3 means the chokepoint doesn't function as intended. Fewer than 1 means the layout offers no alternative and becomes a griefpoint.

**Failure mode (anti-pattern):** Chokepoint inside an arena (corridor-in-the-middle designs). This forces all combat into a single vector and removes tactical depth.

**Procedural note:** In generated maps, chokepoints should be explicitly placed as edges in the layout graph with a "width" attribute. Width < 2× player diameter = hard chokepoint. Width 2–4× = soft. Width > 4× = not a chokepoint.

---

#### 3.4.4 Flanking Routes

**Rule:** Every arena must have at least one flanking route that allows an aggressive player to reach the enemy's rear without crossing the front sightline.

**Exemplar:** CS:GO's Dust II provides 4 main approach paths (Long A, Short A, Mid, B Tunnels) so the attacking team has meaningful choice. Three lanes for a bomb map is the verified sweet spot—two removes attacker options; four overwhelms defenders. ✅ (Gamedeveloper.com FPS layout; WOLD CS:GO guide)

**Test for:** Mark the "enemy starting position" and "player starting position" in the arena. Is there a path between them that does not enter the enemy's default forward sightline? If all paths go through the front sightline, flanking is impossible.

**Failure mode:** Flanking routes that are so dangerous (exposed, no cover) that skilled players never use them. The route exists on the map but not in practice.

---

#### 3.4.5 Verticality

**Rule:** Organize vertical space into at most three distinct floor planes: bottom, middle, top. A fourth plane adds overhead complexity without new tactical dynamics.

**Source:** The Level Design Book verticality chapter, verified against common practice in multiplayer shooters. ✅ (LevelDesignBook verticality; Quake 3 Arena "Longest Yard" analysis)

**Exemplar:** Quake 3's "The Longest Yard" uses jump pads to create strong upward flow while players on stable platforms snipe airborne opponents—the vertical dynamic creates a distinct and memorable tactical proposition. CS:GO maps prefer flat layouts for precision aiming; consoles prefer shallow slopes for analog stick limitations.

**Genre-specific caveat:** Platformers can and should exceed three floor planes—each new platform height is a distinct challenge space. The "max 3 planes" rule applies to combat arenas, not traversal levels.

**Test for:** Cross-section the arena. Count horizontal floors with meaningful tactical differentiation. If you have 4+ floors, audit whether each floor has distinct tactical value or whether two could merge.

**Failure mode:** Meaningless height variation—verticality added for visual interest with identical tactical value at each tier.

---

### 3.5 Pacing Within a Level

#### 3.5.1 The Intensity Curve

**Rule:** Plot your level's intensity as a curve before building it. Peaks require valleys; a level with no valleys produces player fatigue, not engagement.

**Model:** Valve's Cabal sorted beats into four categories: **Explore** (navigate), **Combat** (fight), **Choreo** (scripted sequences), **Puzzle** (find items/unlock doors). Puzzles were explicitly used "to space out combat sequences." ✅ (Gamedeveloper.com Cabal article; Pete Ellis WOLD pacing tutorial)

**Formal encoding—L4D AI Director phases** (machine-readable form of the same principle):
- **Build:** Intensity rising, enemies increasing
- **Peak:** Maximum pressure; normal spawning halts
- **Relax:** 30–45 seconds, no enemy spawns; truncated when survivors resume movement
- **Repeat**

✅ (L4D Director wiki; Valve L4D design documents)

**Test for:** Assign each 60-second segment of your level a 1–5 intensity score. Does the curve have at least one valley (score ≤2) for every peak (score ≥4)? Is the final sequence a peak (not a valley)?

**Failure mode:** "Intensity creep"—each section tries to top the previous one, producing an exhausted player at the boss. The solution is to insert a deliberate low-intensity section 1–2 beats before the final climax.

**Procedural note:** Generate the intensity curve first as a numerical sequence. Assign beats to curve segments (high intensity → combat/hazard rooms; low intensity → exploration/reward rooms). Geometry fills the slots, not the reverse.

---

#### 3.5.2 Rest Beats

**Rule:** Every rest beat must offer at least one of: loot/reward, narrative information, aesthetic pleasure (view/music), or mechanical preview of what's coming. Empty rest beats become skipped sections.

**Exemplar:** Half-Life 2's canoe section on the river provides a visual tour of the world's state—factories, rebels, Combine outposts visible from the water—while requiring only minimal player input. It is an "inhale" that enriches the world rather than halting it. ✅ (intermittentmechanism.blog HL2 analysis)

**Test for:** Can you list what a player gains from every rest beat? If the list is empty, the beat is dead weight.

**Failure mode:** Rest beats that are only "the player walks through an empty room." These become fast-forwarded or skipped on repeat playthroughs and signal pacing weakness in reviews.

---

#### 3.5.3 Reward Placement

**Rule:** Place significant rewards (new weapons, key upgrades, lore reveals) immediately after the highest-intensity section they were earned by, not before it. Pre-emptive rewards undercut tension.

**Exemplar:** Dark Souls places bonfires (the reward/respawn mechanic) after hard sections—not before them. The bonfire's presence after the Taurus Demon tells the player the hard part is over. ✅ (FromSoftware level design analysis; multiple DS1 postmortem sources)

**Test for:** For each major reward in the level, identify the preceding challenge. Is the challenge complete before the reward appears? A reward visible during a challenge is a carrot, not a reward—a different (and sometimes useful) design choice, but not the same thing.

**Failure mode:** Reward directly before a hard section (the player walks into the boss having just looted a chest). The chest makes the boss feel less earned.

---

### 3.6 Process: Blockmesh to Ship

#### 3.6.1 Blockmesh / Greybox First

**Rule:** Build every level in untextured, primitive-geometry blockout before committing any art assets. Expect to rebuild major sections after the first playtest. The blockmesh is disposable by design. ✅ (LevelDesignBook blockout chapter)

**Workflow:**
1. Sketch layout on paper (even rough thumbnail counts)
2. Establish ground plane, scale figure, walls in engine
3. Playtest immediately in-engine (not editor flythrough—physics and camera must be active)
4. Note player deaths, navigation failures, and boring stretches
5. Rebuild the failing sections; repeat from step 3

**Principle:** "Keep it cheap until it is ready to become expensive." ✅ (LevelDesignBook blockout chapter)

**Test for:** Has the level been self-playtested at least 3 complete runs, and has a second person (naive to the design intent) playtested it once, before any art pass begins? If not, the blockout phase is incomplete.

**Failure mode:** Art pass begins on sections the designer is emotionally attached to before playtest confirms those sections work. Sunk cost then prevents necessary structural changes.

---

#### 3.6.2 Metrics

**Rule:** Establish player-body metrics before building any geometry. All architectural dimensions derive from the player capsule.

**Reference table** (verified cross-engine): ✅ (LevelDesignBook metrics)

| Element | Unity | Unreal | Quake / Half-Life |
|---------|-------|--------|-------------------|
| Player bounding box (W × H) | 1.0 × 1.8 m | 60 × 176 cm | 32 × 72 units |
| Player eye height | 1.5–1.7 m | 152 cm | 64 units |
| Min hallway width | 2.0 m | 150 cm | 64 units |
| Door (W × H) | 1.25 × 2.5 m | 110 × 220 cm | 56 × 112 units |
| Wall height | 3.0 m | 300 cm | 128 units |

**Combat range anchors (TF2, verified):** Close ≤256 units; Medium ≤1024 units; Max non-damaging drop: 256 units. ✅ (LevelDesignBook TF2 metrics)

**Test for:** Does every hallway fit two players side by side without clipping? Can the player jump and clear the minimum required obstacle without running start? Are all doors taller than the player's standing eye height by ≥20%?

**Failure mode:** Designers work in "architectural scale" (real human proportions ×1.0) without accounting for camera FOV compression. Spaces designed at 1:1 feel cramped when the game camera is active; add 20–30% scale to interiors.

---

#### 3.6.3 Playtest-Driven Iteration

**Rule:** Observe playtests silently—don't explain, hint, or intervene. Record where players die, where they stop moving, where they look confused, where they express positive surprise. These are design data, not opinions.

**Valve's standard:** 200+ playtesting sessions for Half-Life, generating "100 or so action items" per 2-hour session. Each session observed objective player behavior, not polled opinion. ✅ (Gamedeveloper.com Cabal article)

**Joel Burgess (Fallout 3 / Skyrim, GDC 2014):** The iterative loop is blockmesh → self-test → naive-playtest → fix → repeat. The naive tester's first playthrough is the most valuable data point. ✅ (joelburgess.com GDC 2014 transcript)

**Test for:** Can you list the top 3 failure points in the level by player behavior (not designer intuition)? If the answer comes only from the designer's memory of building the level, no valid playtest has occurred.

**Failure mode:** Asking players "what did you think?" instead of watching them play. Post-play opinions are rationalized, softened, and incomplete compared to live behavior.

---

## 4. Recommendations for the AI Game Designer

### Stage 1 — Structural Design (before any geometry)

1. **Choose structural type** from the table in 3.2.1. Record the type; it constrains all downstream decisions.
2. **Build the mission graph.** Nodes = areas; edges = traversals; conditional edges = locks. Minimum branching factor ≥2 at each decision node for hub-and-spoke or open structures.
3. **Plot the intensity curve** as a numeric sequence (1–5 per beat). Verify at least one valley per peak; verify the curve ends on a peak.
4. **Mark ability/key introduction points.** Every gate (lock edge) must have a corresponding key introduction that precedes it. For Metroidvania structures, verify each ability has 3 uses before its gate is placed.

### Stage 2 — Spatial Design (blockmesh)

5. **Establish player metrics first.** Lock engine, player capsule dimensions, and jump height before placing a single wall.
6. **Place the weenie.** Verify it has line-of-sight from ≥60% of navigable floor area.
7. **Build the critical path in untextured geometry.** Test immediately with player capsule active.
8. **Add wayfinding redundancy:** leading lines (geometry), lighting contrast (signpost lights at beat entrances), breadcrumbs (collectible or detail trail on critical path).
9. **Build arenas with explicit propositions.** State each proposition in a comment/tag before building the geometry.

### Stage 3 — Teaching and Pacing Pass

10. **Audit each mechanic for antepiece.** Every mechanic that appears in a fail-state context must have a consequence-free precursor.
11. **Audit each rest beat for content.** Reward, lore, view, or preview. Empty rest beats are cut or merged.
12. **Verify the Introduce → Develop → Twist → Conclude cycle** is present within each major zone.

### Stage 4 — Iteration Gate

13. **Flag all sections for human playtest** before art pass. Specifically flag: first 90 seconds (do players know where to go?), each new mechanic introduction, each arena, each rest beat.
14. **Document expected behavior** at each flagged section so a human tester can quickly confirm or refute.

### Thresholds That Change the Plan

| Condition | Change |
|-----------|--------|
| Game has no persistent death / low stakes failure | Antepieces become optional (learning by doing is acceptable) |
| Multiplayer / no critical path | Shift all wayfinding work to objective markers and minimap; spatial wayfinding less critical |
| Procedural / infinite levels | Human playtest of specific generated outputs is impractical; substitute automated metric checks (sightline validation, intensity curve scoring, chokepoint width audit) |
| Very short levels (<3 minutes) | Intensity curve may compress to Build→Peak only; valleys become transition moments between rooms rather than distinct sections |
| Open world / sandbox | Weenie density becomes the primary design lever; aim for a weenie visible from any position without fast travel |

---

## 5. Caveats and Known Gaps

**Evidence thin / contested:**

- **"Left-to-right readability" as a formal Valve principle** is widely cited in design discourse but not confirmed in the GDC 2006 HL2 design process PDF or the Cabal article. What is documented is multi-redundant directional cueing (NPCs, lighting, NPC movement). ⚠️ The principle may derive from informal Valve practice not captured in published sources.

- **Intensity curve numerical standards** (e.g., "valley must be ≤2 and peak must be ≥4") are not from primary sources—they derive from the author's synthesis of L4D's Director phases and Pete Ellis's WOLD tutorials. The curve as a qualitative tool is ✅ verified; specific thresholds are ⚠️ unverified.

- **"Three lanes" as a universal competitive FPS standard** is verified for CS:GO and Overwatch maps but not systematically tested across all competitive shooters. Some games (Quake Arena, Doom deathmatch) use different conventions. ⚠️

**AI-authoring risks the literature does not cover:**

- **Sightline validation is 3D and runtime-dependent.** A PCG system working with 2D graphs cannot guarantee sightlines. Generated levels must be validated in a simulation pass with ray-casting from player eye height, not inferred from 2D layout.

- **Antepiece sequencing in infinite/procedural levels.** If the player can reach the same mechanic multiple times in different levels (e.g., a roguelite), the antepiece is needed only once per save file, not once per level. The generator must track which mechanics the player has already encountered.

- **Emotional rest beats require authored content.** A generator can produce a geometrically "empty" room, but the rest beat's quality (narrative, aesthetic, musical) requires hand-authored content in that slot. Procedural systems can schedule the slot; they cannot fill it without authored material.

- **The "arena as sentence" principle requires human judgment** to evaluate. The tactical proposition of an arena cannot be verified by structural analysis alone—it requires a playtest or a model that predicts enemy and player movement. Automated testing can check for cover presence and sightline availability but not for whether the proposition is interesting.

- **Dark Souls-style interconnection requires global spatial knowledge.** The generator must plan shortcut loop edges globally before instantiating geometry, because a shortcut that does not physically intersect with the starting area is geometrically incoherent. Generating rooms independently and connecting them afterward tends to produce shortcuts that "feel" physical but cannot be verified without 3D spatial reasoning.

---

## Sources

**Primary / First-Party:**
- Valve (2006). *Valve's Design Process for Creating Half-Life 2*. GDC 2006. [PDF](https://cdn.akamai.steamstatic.com/apps/valve/2006/GDC2006_HL2DesignProcess.pdf)
- Ken Birdwell (1999). *The Cabal: Valve's Design Process For Creating Half-Life*. Gamedeveloper.com. [link](https://www.gamedeveloper.com/design/the-cabal-valve-s-design-process-for-creating-i-half-life-i-)
- David Shaver (2018). *Invisible Intuition: Blockmesh and Lighting Tips to Guide Players*. GDC Level Design Workshop. [PDF](http://www.davidshaver.net/DShaver_Invisible_Intuition_DirectorsCut.pdf) | [GDC Vault](https://www.gdcvault.com/play/1025179/Level-Design-Workshop-Invisible-Intuition)
- Scott Rogers (2009). *Everything I Learned About Level Design I Learned from Disneyland*. GDC. [GDC Vault](https://gdcvault.com/play/1305/Everything-I-Learned-About-Level)
- Joel Burgess (2014). *The Iterative Level Design Process Used to Ship Fallout 3 and Skyrim*. GDC. [Transcript](http://blog.joelburgess.com/2014/07/gdc-2014-transcript-iterative-level.html)
- Mike Booth / Valve (2009). *The AI Systems of Left 4 Dead*. GDC. [PDF](https://steamcdn-a.akamaihd.net/apps/valve/2009/ai_systems_of_l4d_mike_booth.pdf)

**Reference Books:**
- Christopher Totten (2018). *An Architectural Approach to Level Design* (2nd ed.). Routledge. [Routledge](https://www.routledge.com/Architectural-Approach-to-Level-Design-Second-edition/Totten/p/book/9780815361367)
- Robert Heaton (ed.) / Antonios Liapis. *PCG Book: Constructive Generation Methods for Dungeons and Levels*. [antoniosliapis.com](https://antoniosliapis.com/articles/pcgbook_dungeons.php)

**Online Reference:**
- Robert Yang et al. *The Level Design Book*. [leveldesignbook.com](https://book.leveldesignbook.com) — metrics, blockout, wayfinding, criticalpath, flow, verticality chapters.
- Pete Ellis (WOLD). *Single Player Level Design Pacing and Gameplay Beats* (Parts 1–3). [worldofleveldesign.com](https://www.worldofleveldesign.com/categories/wold-members-tutorials/peteellis/level-design-pacing-gameplay-beats-part1.php)
- Mark Brown / GMTK. *Boss Keys* YouTube series. [YouTube playlist](https://www.youtube.com/playlist?list=PLc38fcMFcV_ul4D6OChdWhsNsYY3NA5B2)
- Boris the Brave (2021). *Lock and Key Dungeons*. [boristhebrave.com](https://www.boristhebrave.com/2021/02/27/lock-and-key-dungeons/)

**Analysis Articles:**
- James Roha (2022). *World Design Lessons from FromSoftware*. Medium. [link](https://medium.com/@Jamesroha/world-design-lessons-from-fromsoftware-78cadc8982df)
- illusorywall (2015). *Dark Souls 1: Distant Views Explained*. Tumblr. [link](https://www.tumblr.com/illusorywall/132589360859/dark-souls-1-distant-views-explained-part-1)
- WOLD (2017). *6 Principles of Choke Point Level Design for Multiplayer Maps as Seen in CS:GO*. [worldofleveldesign.com](https://www.worldofleveldesign.com/categories/csgo-tutorials/csgo-principles-choke-point-level-design.php)
- Gamedeveloper.com (2022). *Analyzing Level Layouts to Improve Level Design in Competitive FPS*. [link](https://www.gamedeveloper.com/design/analyzing-level-layouts-to-improve-level-design-in-competitive-fps)

**Academic:**
- Smith, G. et al. (2011). *Launchpad: A Rhythm-Based Level Design Methodology*. IEEE CIG.
- van der Linden, R. et al. (2013). *Procedural Generation of Dungeons*. IEEE TCIAIG.
- Liapis, A. (PCG Book chapter). *Constructive Generation Methods for Dungeons and Levels*.
