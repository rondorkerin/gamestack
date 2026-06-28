# Changelog

All notable changes to this skill pack are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this
project adheres to [Semantic Versioning](https://semver.org/).

## [0.4.0] — 2026-06-28

### Changed
- **Rebranded `gamedev-skills` → `gamestack`** and repositioned from a game-*design* pack into an **agentic framework for building games on any platform**. The first-party plugin becomes the engine-agnostic *design brain*; the marketplace becomes an *umbrella* that also curates best-in-class community **engine packs** by reference. Plugin dir renamed `plugins/gamedev-skills` → `plugins/gamestack`; all manifests, README, and docs updated; repo homepage now `rondorkerin/gamestack`.

### Added
- **Curated engine packs in the marketplace** (third-party, MIT, referenced by GitHub source — not re-hosted): `godot` ([jame581/GodotPrompter](https://github.com/jame581/GodotPrompter)), `unreal` ([quodsoler/unreal-engine-skills](https://github.com/quodsoler/unreal-engine-skills)), `unity-jahro` ([jahro-console/unity-agent-skills](https://github.com/jahro-console/unity-agent-skills)). Users add one marketplace and install per engine. Unity-general and Three.js coverage are tracked as roadmap gaps.
- `engine-router` (process) — the platform router. Takes a ready design spec and routes implementation to the matching engine pack, keeping design logic and engine code cleanly separated (`SKILL.md`). The glue that makes the framework engine-independent.
- `rpg-systems` (knowledge) — the RPG number-systems triad, from deep research, in three sub-domains: **progression** (price advancement via finite opportunity cost; design the cheapest "use"; don't scale the world to the player — the Oblivion trap its own designer called "a mistake"; bounded location-based level-bands), **economy** (the faucet/transform/drain model; desirable sinks over resented taxes; anti-hoarding under scarcity / the "Elixir problem"; currency-as-crafting-material; never let a market beat playing — the Diablo III RMAH), and **loot** (a stable rarity baseline; build-defining affixes over stat-sticks; item permanence; procedural breadth vs. hand-authored identity; "more loot" as a variable-ratio slot machine; the finite-legendary manifest). Each rule carries exemplars, citations, named failure modes, the procedural/headless implication, and "test-for" criteria; cross-links `game-design-fundamentals`, `combat-design`, `permadeath-and-lethality`, and the procgen skills. Fills the `rpg-systems` slot the pipeline already referenced.
- `systemic-emergent-design` (knowledge) — authoring **affordances, not solutions**, from deep research, in five sub-domains: the **immersive-sim lineage** (universal rules not per-object scripts; Doug Church's intention & perceivable consequence), **emergence from few deep interacting systems** vs. scripted events (Juul's strategy-guide-vs-walkthrough test; interaction density toward S²), the **"good GM" analogy** and its honest limit (multi-solution gates; fail toward lawful resolution), **multiplicative vs. additive design** (Nintendo's chemistry engine; orthogonal affordances; real-world-mapped rules; add a system only if it multiplies), and **emergence × procgen** (generate inputs to systems not finished experiences; freeze a read-only rule substrate; systemic salience to beat Compton's oatmeal). Each rule carries exemplars, citations, named failure modes, the procedural/headless implication, and "test-for" criteria; cross-links `procedural-generation`, `rpg-systems`, `combat-design`, and the procgen skills. Fills the `systemic-emergent-design` slot the pipeline already referenced.

## [0.3.0] — 2026-06-28

### Added
- `game-design-fundamentals` (knowledge) — the spine skill the rest of the pack cross-links to. From deep research: interesting decisions (Sid Meier), flow & difficulty curves (Csikszentmihalyi/Schell), motivation via Self-Determination Theory (competence/autonomy/relatedness), feedback & agency, and choice architecture. Each principle carries exemplars, citations, named failure modes, the procedural/headless implication, and machine-checkable "test-for" criteria written to be enforced as automated invariants in a generation loop. This lights up the forward cross-links the other skills already pointed at.
- `combat-design` (knowledge) — combat & game feel, from deep research, in four sub-domains: **game feel/juice** (the inverted-U dose curve from Kao's N=3,018 study — tune to Medium–High, never maximize; the layered hit-stop/shake/reaction impact bundle), **readability/telegraphing** (telegraphing as a fairness contract, the monotonic damage→cue-salience curve, one game-wide danger vocabulary, player-side responsiveness), **enemy & encounter design** (unique silhouettes, role-based rosters, the "door problem of combat design", readable chaos via aggression tokens, curated shared movesets, story-shaped pacing), and **Souls-like lethality** (animation commitment + stamina, death-as-teacher, no build-lock, bounded comeback mechanics, generous checkpoint loops). Each rule carries exemplars, citations, named failure modes, the procedural/headless implication, and "test-for" criteria; cross-links `game-design-fundamentals`, `permadeath-and-lethality`, `rpg-systems`, and the procgen skills. Fills the `combat-design` slot the pipeline and fundamentals already referenced.

## [0.2.0] — 2026-06-28

### Changed
- **Repositioned the pack as a game-design *process* pack** (the gstack model for software, applied to game design). Two skill layers now: **knowledge** (cited disciplines) and **process** (workflow verbs an agent runs). Tuned for headless, engine-agnostic (Godot), procedural / AI-authored development. Stops at the design/spec level — no gameplay code. README, plugin, and marketplace metadata updated.
- Rewrote `open-world-design` (v0.1 → v0.2) from the Open-World RPG Design Bible: the triangle rule, gravity & flow, interconnection, biome identity, verticality, diegetic navigation, signal color, exploration pull, spatial pacing, visual orientation — with citations and per-rule "test-for" criteria.

### Added
- `game-design-process` (process) — the orchestrator. Walks concept → world & systems → content → review → playtest/iterate, names the skill for each phase, with entry/exit gates and replan thresholds (`PIPELINE.md`).
- `procedural-generation` (knowledge) — beating the "10,000 bowls of oatmeal" problem: perceptual uniqueness, the handcrafted-anchor + constrained-fill hybrid, voice-consistent corpora, intentionality/local logic, multiplicative systems, curated randomness.
- `procgen-review` (process) — the flagship self-review gate for generated content. Five gates (oatmeal, fanfic/retell, cross-instance sameness, intentionality, anti-pattern), a structured JSON verdict, and headless generate→review→repair loop integration (`REVIEW.md`).

## [0.1.0] — 2026-06-28

### Added
- Initial release of the `gamedev-skills` plugin and marketplace.
- `open-world-design` skill (v0.1): guide + do/don't checklist covering world
  layout, points-of-interest density, navigation & wayfinding, the
  explore-reward loop, traversal, systemic/emergent design, and the
  "icon vomit" anti-pattern. Marked for expansion as research lands.
- Repo scaffolding: README, CONTRIBUTING, MIT license.
