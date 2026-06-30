# gamestack architecture — the tiered, multi-pack design brain

> Status: **decided 2026-06-30.** Supersedes the implicit "one flat pack tuned for a
> procgen open-world RPG" structure. This document is the source of truth for how the
> first-party design brain is organized as it grows to cover general game development.

## The problem this solves

The pack began as a deep, excellent design brain for **one game** — Valenfeld, a procedural
open-world action-RPG — and that single running example leaked into the framework's bones:

- The orchestrator's Phase 2 is literally *"World & systems"* and assumes an open world
  with biomes and navigation. A puzzle, fighting, strategy, or card game doesn't enter there.
- Of the original 11 knowledge skills, only **2 are truly universal**
  (`game-design-fundamentals`, `art-direction-and-readability`). `combat-design` is half
  universal (game feel is buried inside it). The other 8 are open-world / RPG / procedural /
  immersive-sim **lenses**.
- The biggest **universal craft** disciplines were absent: level design, game feel as its own
  thing, onboarding & teaching, UX/UI & HUD, difficulty & balancing, pacing, audio,
  accessibility, playtesting & telemetry, monetization, scope & production.

"General game dev" is therefore not a few more skills — it is a **missing layer**. The pack
had genre lenses and technique modules sitting on a thin universal spine.

## The three tiers

Every design skill belongs to exactly one tier. The tiers are the axes that were previously
tangled into one flat list.

| Tier | Applies when | A skill here is… |
|------|--------------|------------------|
| **Universal craft** | *every* game, regardless of genre or technique | the "what good looks like" that no genre escapes — feel, teaching, challenge, space, interface, rhythm |
| **Genre lens** | you are making *that kind* of game | the design knowledge specific to a genre family (RPG, platformer, shooter, strategy, sim, deckbuilder…) |
| **Technique module** | you are *using* that technique | cross-genre implementation philosophy (procgen, AI-authored coherence, systemic/emergent, multiplayer/netcode) |

### Current skills, re-tiered

**Universal craft** — `game-design-fundamentals`, `art-direction-and-readability`,
and (after extraction, see below) `game-feel-and-juice`.

**Genre lens** — `open-world-design`, `rpg-systems`, `permadeath-and-lethality`,
`narrative-and-quest-design`, `combat-design` (action-combat lens),
`worldbuilding-and-lore` (narrative-genre lens).

**Technique module** — `procedural-generation`, `ai-authored-content-coherence`,
`systemic-emergent-design`.

**Process** (tier-spanning, lives in core) — `game-design-process`, `engine-router`,
`procgen-review`.

## Packaging: separate marketplace plugins

The design brain ships as **layered plugins**, mirroring the existing "design brain / engine
hands" split. Install only what your game needs.

```
gamestack-core        always installed — the universal craft brain + the process spine
  ├─ universal craft skills (fundamentals, level-design, game-feel, onboarding,
  │    ui-ux, difficulty-balancing, pacing, art-direction, audio, accessibility, …)
  ├─ game-design-process  (genre-aware orchestrator)
  ├─ engine-router
  ├─ procgen-review
  ├─ ETHOS.md · shared/PREAMBLE.md · shared/GATE.md · overlays/
  └─ the design bible contract (.gamestack/bible/)

gamestack-rpg         genre pack — rpg-systems, open-world-design, worldbuilding-and-lore,
                        narrative-and-quest-design, permadeath-and-lethality
gamestack-action      genre pack — combat-design, (future: shooter, platformer/movement, fighting)
gamestack-strategy    genre pack — (future: RTS/4X/tactics, economy-heavy sim)
gamestack-sim         genre pack — (future: management, builder, survival/crafting)
gamestack-puzzle      genre pack — (future: puzzle, deckbuilder)
…                     one pack per genre family, added as genres are researched

gamestack-procgen     technique pack — procedural-generation, ai-authored-content-coherence,
                        systemic-emergent-design
gamestack-multiplayer technique pack — (future: netcode/sync, matchmaking, live-ops design)

godot / unreal / unity / threejs   engine hands (existing, third-party, referenced)
```

The exact genre-pack groupings firm up as the genre research lands; `gamestack-core` and the
core/genre/technique split are the committed decisions.

### Why multi-pack and not one tiered monolith

- A puzzle-game dev should not carry (or have the agent reason over) RPG, open-world, and
  permadeath knowledge. Install-what-you-build keeps each agent's skill surface relevant.
- The marketplace already supports N plugins and already does this for engine hands.
- It gives a clean story: *"install the genres you're building."*

### The cost multi-pack imposes (and how core handles it)

The orchestrator in `gamestack-core` may name a skill that lives in a genre pack the user has
not installed. Two rules keep this graceful:

1. **Core never hard-depends on a genre/technique pack.** It references them by name and
   degrades: *"this is a strategy game — the `gamestack-strategy` pack would deepen this; it
   isn't installed, so I'll apply the universal-craft principles and flag the gap."*
2. **Genre packs depend on core, never on each other.** Cross-genre references go through the
   universal-craft skills in core, not sideways between genre packs.

## The orchestrator becomes genre-aware

`game-design-process` Phase 2 stops being *"build a world."* The new shape:

1. **Phase 1 · Concept** (universal) — fantasy, pillars, core loop, signature mechanics.
2. **Classify the genre** — from the concept, name the genre family (or families) and record
   it in the bible. This selects which genre lens(es) and technique module(s) to pull.
3. **Phase 2 · Build the genre's core** — pull the universal spine (level design, game feel,
   difficulty, pacing, UX, onboarding) **plus** the matching genre lens. For an open-world
   RPG that is `open-world-design` + `rpg-systems`; for a platformer it is the
   movement/level-design lens; for a deckbuilder it is the card/economy lens. The *universal*
   skills are always in play; the *genre* skills are selected.
4. **Phase 3 · Content**, **Phase 4 · Review & gate**, **Phase 5 · Playtest** — as today,
   with technique modules pulled when the game is procedural / AI-authored / multiplayer.

The bible gains a `genre` record alongside `engine`, set during classification.

## Build order

1. **Round 1 — universal spine (in progress):** level-design, game-feel-and-juice,
   onboarding-and-teaching, ui-ux-and-feedback, difficulty-and-balancing,
   pacing-and-the-player-journey. Research prompts in `docs/research-prompts.md`.
   - Extract the game-feel/juice material out of `combat-design` into the new universal
     `game-feel-and-juice`; `combat-design` keeps the combat-specific application and links to it.
2. **Round 2 — finish the universal spine:** audio design, accessibility, playtesting &
   telemetry, monetization & business model, scope/production & vertical slice.
3. **Round 3+ — genre breadth:** platformer/movement, shooter, puzzle, strategy/tactics,
   sim/management, survival/crafting, deckbuilder, horror, racing/rhythm/fighting,
   adventure/VN — one genre pack at a time.
4. **Technique breadth:** multiplayer/netcode design, save/persistence, physics/sim.
5. **Repackage** the repo into the plugin layout above once round 1 lands and the
   core/genre/technique membership is proven by use.

Until the physical repackage, skills are authored into the existing `plugins/gamestack/skills/`
tree and *tagged* with their tier in `SKILL.md`, so the later split is a move, not a rewrite.
