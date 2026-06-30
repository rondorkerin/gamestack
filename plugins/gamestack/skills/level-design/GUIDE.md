# Level Design — Guide

The craft of making a level guide, teach, pace, and validate itself. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from Valve's Cabal article (Ken Birdwell 1999), GDC 2006 HL2 design process, David Shaver GDC 2018 "Invisible Intuition," Scott Rogers GDC 2009, Joel Burgess GDC 2014, Mike Booth/Valve L4D AI systems, Mark Brown's Boss Keys (GMTK), illusorywall/James Roha DS1 analysis, Christopher Totten's *An Architectural Approach to Level Design* (2nd ed.), Smith et al. LaunchPad (2011), and *The Level Design Book* (leveldesignbook.com). Verification tags inline: ✅ ≥2 independent/primary sources · ⚠️ single/contested · ❌ refuted. Full citations in `docs/research/round1-universal/U1-level-design.md`.

> **The central law:** *guide before you challenge; teach through space, not text; pacing is a rhythm of peaks and valleys; blockmesh until it works, then commit art.* A level that challenges before it guides is unfair; one that never rests is exhausting; one artworked before validated is expensive to fix.

> For the combat-specific application of arenas (hit-stop, telegraphing, enemy feel) see `combat-design`. For the session-wide arc see `pacing-and-the-player-journey`. For the macro/world spatial layer see `open-world-design`. For signposting, signal color, and landmarks as art see `art-direction-and-readability`.

---

# Sub-domain A — Guidance Language

> Left 4 Dead's designers state paths are "implied by light placement" as the primary channel, with geometry, color contrast, NPC movement, and sound as backups. **Single-channel wayfinding fails** players with accessibility needs and fails procedural layouts that can't guarantee one channel is always visible. ✅ (Shaver GDC 2018; LevelDesignBook wayfinding)

## A.1 Define the critical path first, as a graph

The critical path visualizes the beat map for collaborators and scopes work. Annotate your layout sketch with numbered beats and player-movement arrows *before* placing any geometry.

- **Rule:** Starting from spawn, a player must reach the exit using only afforded routes (no clipping, no designer knowledge) in under 30 seconds of continuous movement. ✅ (Valve Cabal article; LevelDesignBook criticalpath)
- **Test for:** Naive player finds exit in <30 seconds without clipping?
- **Failure mode — Path starvation:** Designers optimize visually impressive dead-ends while the main path has no wayfinding resources. Players find impressively lit dead-ends and miss the exit.

## A.2 Breadcrumbing and leading lines: a line-of-sight chain

Place collectibles, environmental details, or architectural lines (tracks, fences, roads, lighting rakes) along the critical path — each crumb visible from the previous one at player eye height. ✅ (LevelDesignBook wayfinding; DKC banana trails; HL2 opening NPC movement + lighting)

- **Test for:** Can a first-time player identify the next waypoint within 3 seconds of entering any new space? Screenshot at player eye height — do lines converge on the next beat?
- **Failure mode — Breadcrumbs at designer speed:** Crumbs placed at the designer's fast navigation pace cluster behind the player's field of view. Leading lines added in an art pass terminate on walls instead of goals.

## A.3 Landmarks (weenies): the constant orientation anchor

Place ≥1 large, visually distinctive landmark with near-constant line of sight that serves as the zone's overarching pull. ✅ (Scott Rogers GDC 2009 "Disneyland"; HL2 Citadel tower — visible from most city chapters, always indicating Gordon's direction)

- **Test for:** From any navigable position in the level, is at least one large structural landmark visible that unambiguously indicates the direction of progress?
- **Failure mode — Occluded weenie:** Procedural geometry accumulation or fog blocks the landmark in exactly the areas where players most need orientation.
- **Procedural note:** Reserve a non-occluded height or silhouette slot. For top-down or isometric games, the weenie is often a distinctive biome or building cluster.

## A.4 Light, color, contrast, and affordances as signpost

Light exits, doors, and interactive objects more brightly or with a contrasting hue than surroundings. Every interactable must communicate what it does before the player commits. ✅ (Shaver GDC 2018; LevelDesignBook composition)

- **❌ Refuted:** "Players always look toward the brightest spot" — false when enemies, fire, or VFX are also bright. Light works as signpost only when it is *contrastive in context*, not merely the brightest thing on screen.
- **Test for:** Desaturate a screenshot — are critical elements still distinguishable by value alone (not hue only)? Cover the UI: does the player attempt the intended interaction within 10 seconds using only environmental information?
- **Failure mode — Decoration as interactable:** Generic-looking crates as puzzle switches; players walk past. Novel interactables require a hand-authored introduction.

**→ Procedural / headless implication.** Generate the critical path as a graph first (start → beat₁ → beatₙ → exit), then instantiate geometry around each node. Encode breadcrumb waypoints so each has line-of-sight from the previous at player eye height (not inferred from top-down). Assign a "signpost" lighting layer separate from ambient/mood; place signpost lights at every beat entrance before any other light pass. Maintain an affordance vocabulary — generated content instantiates interactables from approved pairs only; novel interactables need hand-authored antepieces before they appear in stakes contexts.

---

# Sub-domain B — Structure & Gating

> Structural type is a pacing contract with the player. Choose it before layout; it constrains every downstream decision. ✅ (LevelDesignBook criticalpath; multiple structural analyses)

| Structure | Pacing control | Player agency | Canonical example |
|-----------|---------------|---------------|-------------------|
| **Linear** | Maximum | Minimal | Half-Life, CoD campaign |
| **Multi-path linear** | High | Moderate | Halo CE, Dishonored |
| **Hub-and-spoke** | Medium | High within spokes | Dark Souls (Firelink), Zelda dungeons |
| **Open world** | Low | Very high | Skyrim, RDR2 |

## B.1 Model gating as a mission graph

Represent progression as nodes (areas), edges (traversals), and conditional edges (locks requiring a key). A lock that resolves to branching factor 1 in the graph is decorative, not structural. ✅ (GMTK Boss Keys; boristhebrave.com lock-and-key)

- **Exemplar:** Mark Brown's Boss Keys graphs Zelda dungeons: original *Zelda*'s dungeons are dense branching graphs — players can pursue multiple keys simultaneously. *Phantom Hourglass* collapses to near-linear chains. The graph makes the difference visible instantly.
- **Test for:** Convert the level to a mission graph. Does the player ever have ≥2 meaningful choices at a node? If every node has branching factor 1, the structure is a maze dressed as a choice.
- **Failure mode — Fake complexity:** A maze-like floor plan that resolves to one valid path when graphed. Players feel trapped and cheated.

## B.2 Interconnection and shortcuts: collapse the mental map

Design ≥1 shortcut per major zone that reduces a long traversal to seconds. Announce the shortcut from the *destination* side — player sees the locked door before finding the key. ✅ (illusorywall DS1 analysis; James Roha Medium; thegamer.com DS1 level design)

**Three rules from DS1 analysis:**
1. Shortcut opens from one end only on first approach — maintains cost on the initial traversal.
2. Shortcut links two areas the player has already seen — it does not introduce new space.
3. Opening is narratively legible (gate, lever, ladder) — not a magic portal.

- **Test for:** After completing the zone normally, can a player traverse it again in ≤20% of the original time using available shortcuts?
- **Failure mode:** Shortcuts placed at the designer's "cool moment" preference rather than at high-friction traversal points. Players don't need shortcuts where movement is already fast.

## B.3 Metroidvania loop: every ability earns three uses

Every new ability must enable: (1) progress past the current gate, (2) shortcut through earlier space, (3) reframe a combat or puzzle space. An ability that enables only use #1 is a key, not an upgrade. ✅ (Dreamnoid Metroidvania guide; *Super Metroid* Grapple Beam / *Hollow Knight* Crystal Heart analysis)

- **Test for:** For each new ability, list three distinct uses across three different areas before placing its gate. Can't find all three? The ability is under-designed.
- **Failure mode — Ability bloat:** Later abilities supersede earlier ones with no backtrack payoff. The map feels linear even though it's physically connected.

**→ Procedural / headless implication.** Generate the mission graph first with a minimum branching-factor constraint (≥2 at decision nodes for hub-and-spoke structures), then derive geometry. Encode shortcuts as "bonus edges" that activate after flag conditions — guarantee ≥1 per major hub-to-spoke traversal. For Metroidvania structures, maintain an ability dependency graph; new ability events must trigger re-evaluation of accessible areas to surface previously gated content.

---

# Sub-domain C — Teaching Through Space

> **Teach through space, not text.** The antepiece pattern is the most-proven teaching tool in level design, used consistently from *Super Mario Bros.* to *Portal* to *Dark Souls*. Explicit tutorials fail players who skip them; antepieces embedded in normal level space cannot be skipped. ✅ (Nintendo level design; Valve Cabal; Portal test-chamber design guide)

## C.1 The antepiece: safe introduction before the lethal test

Before a mechanic is tested at high stakes, introduce it in a consequence-free environment — no other variables active, zero cost to failure.

- **Exemplar 1:** *Super Mario Galaxy 2*'s flipping platforms appear above solid ground before appearing above a chasm. ✅ (TVTropes Antepiece; Nintendo level design analysis)
- **Exemplar 2:** *Zelda: Skyward Sword*'s first Timeshift Stone is in a hazard-free clearing; all subsequent stones are adjacent to quicksand or spikes. ✅ (TVTropes Antepiece)
- **Exemplar 3:** *Portal*'s first chambers have no wrong way to use a cube before cubes become load-bearing puzzle elements. ✅ (Portal test-chamber design guide; portalwiki.net)
- **Test for:** Does every mechanic have an antepiece preceding its first lethal encounter? If the first encounter is a fail state, the antepiece is missing.
- **Failure mode:** Tutorial rooms visually separate from the game world break immersion. Embed the antepiece in normal-looking level space.

## C.2 Introduce → Develop → Twist → Conclude

Within a single zone, cycle a mechanic through four phases: safe first encounter → escalation (adds variables) → unexpected application (twist using the mechanic a new way) → resolution under full load. ✅ (Nintendo level design; kishoutenketsu structure; Gamedeveloper.com SMW analysis)

- **Exemplar:** *Super Mario World* Yoshi levels: eats shell (Introduce) → shell breaks a barrier (Develop) → spit bouncing shell at a moving target (Twist) → shell-riding + targeting + platforms combined (Conclude).
- **Test for:** Map each section to one of the four phases. Do all four appear in order? Can a player who fails the Twist retry it from a checkpoint without replaying Introduce/Develop?
- **Failure mode:** The Twist placed at the final gate before the exit — failure means replaying the entire level, punishing experimentation and blocking mastery.

## C.3 The gym: isolated single-type encounters first

For games with rich combat, provide a low-stakes zone where each enemy type is encountered individually before mixed-type groups appear. ✅ (Valve Cabal; HL2 Lambda Core; Halo CE encounter design)

- **Test for:** Can a player encounter and defeat each enemy type in an isolated single-type encounter before facing mixed groups?
- **Failure mode:** Gym areas too safe — they never teach the actual danger of the enemy. Players who "complete the gym" are surprised by lethality in real combat.

**→ Procedural / headless implication.** The generator must know which mechanic each room features and guarantee mechanic first-appearance in a consequence-free slot — a *sequencing constraint*, not a content constraint. For roguelites/infinite levels, track which mechanics the player has already encountered per save file; the antepiece is needed once per file, not once per level. Beat sequences must include flags for all four I→D→T→C phases; a level missing Twist will feel repetitive; one starting with Twist will feel unfair.

---

# Sub-domain D — Encounter & Arena Design

> Each combat arena should communicate a single tactical proposition — one or two meaningful choices for the player. The geometry is the argument; the player's movement through it is the resolution. ✅ (Gamedeveloper.com DOOM 2016 analysis; Fullbright FEAR encounter analysis)

## D.1 Arena as a sentence

State the arena's tactical proposition in one sentence before building geometry. ("This arena punishes camping — the only ammo spawns are at the far end." "This arena rewards high ground — the sniper rifle is at elevation.")

- **Exemplar:** DOOM 2016 arenas are circular or figure-eight with multiple height tiers — standing still produces death (enemies pressure all vectors) while constant movement cycles through cover nodes. The geometry enforces the "push forward" loop without stating it in a tutorial. ✅ (Gamedeveloper.com DOOM 2016 analysis)
- **Test for:** Can you describe the arena's proposition in one sentence? If not, the arena has no proposition and produces neutral, forgettable space.
- **Failure mode — Spectacle over proposition:** Impressive architecture with uniform flat ground and no cover variation.

## D.2 Cover, chokepoints, and flanking routes

Every arena needs both half-height cover (enables firing over) and full-height cover (enables concealment), ≥3 positions with distinct lines of fire. Chokepoints go *between* arenas. Every arena needs ≥1 flanking route reaching the enemy's rear without crossing the forward sightline. ✅ (Fullbright FEAR analysis; WOLD CS:GO chokepoint guide; Gamedeveloper.com FPS layout)

- **Three-lane sweet spot:** For bomb-map competitive FPS, three lanes is the verified optimum — two removes attacker choice, four overwhelms defenders. ⚠️ (Verified for CS:GO and Overwatch; not universally tested across shooters.)
- **Chokepoint timing:** Defenders should reach the chokepoint ~5–12 seconds before attackers. ✅ (WOLD 6 Principles CS:GO Chokepoints)
- **Test for (cover):** Top-down silhouette — ≥3 distinct cover positions with different lines of fire toward enemy start positions? **Test for (chokepoint):** Count viable attacker entry vectors: >3 = doesn't function; <1 = griefpoint. **Test for (flanking):** Is there a path to the enemy's rear that avoids all default forward sightlines?
- **Failure mode — Cover theater:** Cover placed perpendicular to enemy fire — looks good in screenshots, provides no actual protection.

## D.3 Verticality: cap at three tactical floor planes

Organize combat arenas at at most three distinct floor planes with meaningful tactical differentiation. A fourth adds overhead complexity without new dynamics. ✅ (LevelDesignBook verticality; Quake 3 "Longest Yard" analysis)

- **Genre caveat:** This applies to *combat arenas*. Platformer traversal levels can and should exceed three planes — each new height is a distinct challenge space.
- **Test for:** Cross-section the arena. Does each floor plane have a distinct tactical proposition, or could two merge without loss?
- **Failure mode:** Meaningless height variation — verticality for visual interest with identical tactical value at each tier.

**→ Procedural / headless implication.** Tag each generated arena with its stated proposition before instantiating geometry. Automated testing can check cover presence (≥3 positions), chokepoint width (< 2× player diameter = hard chokepoint; 2–4× = soft; >4× = not a chokepoint), flanking path existence, and floor-plane count. Automated testing cannot verify whether the proposition is *interesting* — flag arenas for human review; the proposition tag is the review contract.

---

# Sub-domain E — In-Level Pacing

> Pacing is a rhythm of peaks and valleys. Sustained high intensity produces fatigue, not excitement. Left 4 Dead's AI Director codifies this as a machine; hand-designed levels must encode it as spatial sequencing. ✅ (Valve L4D AI systems GDC; Pete Ellis WOLD pacing tutorial)

## E.1 Plot the intensity curve before you build

Assign each 60-second segment a 1–5 intensity score. Verify ≥1 valley (≤2) per peak (≥4); the final segment must be ≥4.

**L4D AI Director phases — the machine-readable form:** Build (intensity rising) → Peak (max pressure, normal spawning halts) → Relax (30–45 s, no spawns, truncated when survivors resume movement) → repeat. ✅ (Valve Mike Booth GDC L4D AI systems)

- **Test for:** Does the curve end on a peak? Is there ≥1 valley between each consecutive peak pair?
- **Failure mode — Intensity creep:** Every section tops the previous, producing an exhausted player at the boss. Fix: insert a deliberate low-intensity beat 1–2 sections before the final climax.

## E.2 Rest beats must earn their space

Every rest beat must offer ≥1 of: loot/reward, narrative information, aesthetic pleasure (view/music), or mechanical preview of what's coming. Empty rest beats become skipped on replay.

- **Exemplar:** HL2's river canoe section provides a visual tour of the world state while requiring minimal player input — an "inhale" that enriches the world rather than halting it. ✅ (intermittentmechanism.blog HL2 analysis)
- **Test for:** For every rest beat, list what a player gains. If the list is empty, the beat is dead weight — cut or merge it.
- **Failure mode:** Geometrically empty rooms. On repeat playthroughs, players fast-forward them; reviewers flag the pacing weakness.

## E.3 Reward placement: after the earned peak, not before

Place significant rewards (new weapons, key upgrades, lore reveals) immediately *after* the highest-intensity section they were earned by. Pre-emptive rewards undercut tension. ✅ (DS1 bonfire placement; multiple FromSoftware postmortem analyses)

- **Exemplar:** Dark Souls places bonfires after hard sections — the bonfire after the Taurus Demon signals "the hard part is over." A chest directly *before* a boss makes the boss feel less earned.
- **Test for:** For each major reward, is its preceding challenge fully complete before the reward appears? A reward visible *during* the challenge is a carrot (sometimes intentional) — that is a different design choice, not a mistake, but it should be deliberate.
- **Failure mode:** Reward before the hard section. Player enters the boss fight already compensated; tension arc flattens.

**→ Procedural / headless implication.** Generate the intensity curve first as a numeric sequence; assign beat types to curve slots (high intensity → combat/hazard rooms; low → exploration/reward rooms); geometry fills the slots, not the reverse. The generator can schedule rest-beat slots; it cannot fill them with quality narrative, aesthetic, or musical content without hand-authored material assigned to that slot. Flag rest beats for authored content; reject rest beats that are geometrically empty with no authored material assigned.

---

# Sub-domain F — Greybox Process

> "Keep it cheap until it is ready to become expensive." The blockmesh is disposable by design. Committing art before validating layout is the most expensive mistake in level design. ✅ (LevelDesignBook blockout chapter)

## F.1 Blockmesh first, playtest in engine, iterate

**Workflow:**
1. Sketch layout on paper (even a rough thumbnail counts)
2. Establish ground plane, scale figure, walls in engine
3. Playtest immediately in-engine — physics and camera active; not editor flythrough
4. Note player deaths, navigation failures, boring stretches
5. Rebuild failing sections; repeat from step 3

✅ (LevelDesignBook blockout; Joel Burgess GDC 2014 iterative level design)

- **Test for:** Has the level been self-playtested ≥3 complete runs, and has ≥1 naive person playtested it once, before any art pass begins?
- **Failure mode:** Art pass begins on sections the designer is emotionally attached to before playtest confirms they work. Sunk cost blocks necessary structural changes.

## F.2 Player metrics anchor all geometry

Establish player capsule dimensions before placing any wall. All architectural dimensions derive from the player body. ✅ (LevelDesignBook metrics; TF2 Valve design docs)

| Element | Unity | Unreal | Quake / Half-Life |
|---------|-------|--------|-------------------|
| Player bounding box (W×H) | 1.0 × 1.8 m | 60 × 176 cm | 32 × 72 units |
| Player eye height | 1.5–1.7 m | 152 cm | 64 units |
| Min hallway width | 2.0 m | 150 cm | 64 units |
| Door (W×H) | 1.25 × 2.5 m | 110 × 220 cm | 56 × 112 units |
| Wall height | 3.0 m | 300 cm | 128 units |

- **Test for:** Does every hallway fit two players side by side without clipping? Can the player clear the minimum required obstacle without a running start?
- **Failure mode:** Working at 1:1 architectural scale without accounting for camera FOV compression. Interiors feel cramped in-engine; add 20–30% scale to compensate.

## F.3 Playtest silently — behavior over opinion

Observe playtests without explaining, hinting, or intervening. Record where players die, where they stop, where they look confused, where they express positive surprise. Post-play opinions are rationalized, softened, and incomplete. ✅ (Valve Cabal: 200+ sessions for HL1; Joel Burgess GDC 2014)

- **Test for:** Can you list the top 3 player failure points by observed behavior — not designer intuition? If the answer comes only from memory of building the level, no valid playtest has occurred.
- **Failure mode:** "What did you think?" instead of watching them play. Post-play opinion yields rationalized, softened, incomplete data.

**→ Procedural / headless implication.** For procedural/infinite levels, human playtest of every generated output is impractical. Substitute: sightline validation (ray-cast from player eye height at each beat to the next and from navigable floor to the weenie), intensity-curve scoring (valley-per-peak constraint enforced), chokepoint width audit, antepiece sequencing validation (mechanic first appearance in consequence-free slot), and player-body metric conformance checks. Flag outputs passing all automated checks for *sampled* human playtest — compress human observation, don't replace it.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Path starvation | Main path dark; players find lit dead-ends | Wayfinding resources on the critical path first (A.1) |
| Breadcrumbs at designer speed | Crumbs cluster behind player FOV | Line-of-sight chain at player navigation pace (A.2) |
| Occluded weenie | Landmark blocked by procgen geometry | Reserve non-occluded height/silhouette slot for landmark (A.3) |
| Decoration as interactable | Generic crate is the puzzle switch | Affordance vocabulary; hand-authored novel introductions (A.4) |
| Fake complexity | Maze floor plan resolving to chain-of-one | Mission graph with branching factor ≥2 at decision nodes (B.1) |
| Ability bloat | Later abilities make earlier areas irrelevant | Three-use test for every ability before gating (B.3) |
| Tutorial segregation | Training grounds separate from game world | Embed antepiece in normal-looking level space (C.1) |
| Twist at the exit | Fail = replay whole level | Checkpoint before Twist; Twist before final gate (C.2) |
| Spectacle arena | Gorgeous flat coliseum, no tactical proposition | One-sentence proposition before building geometry (D.1) |
| Cover theater | Cover perpendicular to enemy fire | ≥3 cover positions with distinct fire angles (D.2) |
| Intensity creep | Every section tops the previous one | Deliberate valley 1–2 beats before the climax (E.1) |
| Empty rest beats | Geometrically empty room | Must contain reward / lore / view / preview (E.2) |
| Pre-emptive reward | Chest before boss | Reward after the earned peak (E.3) |
| Art before playtest | Art pass on unvalidated layout | ≥3 self-runs + ≥1 naive tester before art (F.1) |
| Asking instead of watching | "What did you think?" | Silent observation; behavior is the data (F.3) |

---

## Caveats

- **"Left-to-right readability" as a formal Valve rule** is widely cited but not confirmed in the GDC 2006 HL2 design-process PDF or the Cabal article. What is documented is multi-redundant directional cueing (NPCs, lighting, NPC movement). ⚠️ Treat as informal practice, not a verified principle.
- **Intensity curve thresholds** (valley ≤2, peak ≥4) are the author's synthesis of L4D's Director phases and Pete Ellis WOLD tutorials. The curve as a qualitative tool is ✅; specific numerical thresholds are ⚠️ and need per-title calibration.
- **"Three lanes" in competitive FPS** is verified for CS:GO and Overwatch but not systematically tested across all competitive shooters. ⚠️
- **The "arena as sentence" proposition** cannot be verified by structural analysis alone — it requires a playtest or a movement-prediction model. Automated validators can check cover presence and sightline availability; they cannot determine whether the proposition is *interesting*.
- **Dark Souls-style interconnection requires global spatial knowledge.** A generator must plan shortcut loop edges globally before instantiating geometry; generating rooms independently and connecting them afterward tends to produce shortcuts that are physically incoherent without 3D spatial reasoning.
- **Emotional rest beats require authored content.** A generator can schedule a geometrically "empty" slot; filling it with quality narrative, aesthetic, or musical content requires hand-authored material.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
