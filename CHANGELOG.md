# Changelog

All notable changes to this skill pack are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this
project adheres to [Semantic Versioning](https://semver.org/).

## [0.6.0] — 2026-06-28

### Added
- **Architected the framework like gstack (workflow-spine phase).** From a `/plan-ceo-review` pass (SELECTIVE EXPANSION) that survived two rounds of adversarial spec review (4/10 → 8/10). Copies gstack's *shape*, not its plumbing:
  - `ETHOS.md` — the gamestack builder ethos (systems over content, no 10,000 bowls of oatmeal, hand-author the spine, gate before commit, design is engine-independent, interesting decisions, steal the fun, designer sovereignty). Loaded into every process skill.
  - `shared/PREAMBLE.md` — a shared preamble injected at the top of each process skill via the docs-verified `` !`cat ${CLAUDE_PLUGIN_ROOT}/...` `` load-time mechanism (single source, no build step — this is cheaper than gstack's `SKILL.md.tmpl` generator, which gamestack therefore does not need). It loads the design bible, detects the engine + overlay, states the completeness principle, and declares the completion-status protocol. Falls back to a Read instruction when `disableSkillShellExecution` is set.
  - **The design bible** (`<project>/.gamestack/bible/`: `pillars/world/systems/lore/constraints.md`, `corpus/`, `engine`, `decisions.md`) — gamestack's persistent cross-session memory and the engine-handoff contract. The gbrain analog.
  - `shared/GATE.md` — the headless generate→review→repair loop contract: `procgen-review` emits its verdict as the final fenced ```json block; an external `claude -p` harness (bounded retries) interprets pass/fail. Exit code belongs to the harness, not the skill.
  - Per-engine overlays `overlays/{godot,unity,unreal,threejs}.md` — gstack's model-overlay pattern applied to engines; each maps design specs to the engine pack's skills (Unity-general and Three.js flagged as gaps).

### Changed
- `game-design-process` is now the **first-touch router + orchestrator + bible owner** (collapsing what had drifted toward three overlapping dispatchers into two: this skill sequences design; `engine-router` does the engine handoff only).
- `procgen-review` gained the machine-readable verdict contract for the GATE loop.
- `engine-router` now consumes the bible and the loaded overlay, and reports completion status.
- The plan that produced this lives at `~/.gstack/projects/rondorkerin-gamestack/ceo-plans/`. Deferred to a later phase: a `bin/` runtime (config/telemetry/learnings), a versioned upgrade flow, structured `decisions.jsonl`, and a `playtest-review` verb. Cancelled: a doc-generation toolchain (the `!cat` injection removes the need).

## [0.5.0] — 2026-06-28

### Added
- `narrative-and-quest-design` (knowledge) — quests, reactivity, and factions, from deep research (topic 7). Verified core: the **facts-database** reactivity substrate (Witcher 3 / REDkit — every branch a Condition over a single canonical fact store), **planner-over-world-state** procedural quests (CONAN, arXiv:1808.06217), and deliberately **blurring flavor vs. consequence** (Tomaszkiewicz). Sourced-but-unverified doctrines (tagged ⚠️): the no-fetch-quest rule, radiant-as-supplement-not-substitute (Skyrim), and faction allegiance dilemmas (New Vegas / Morrowind). Includes a `❌ do-not-encode` section for a refuted absolute ("quests never fail on player choice", refuted 0-3). Cross-links `game-design-fundamentals`, `open-world-design`, `procgen-review`.
- `art-direction-and-readability` (knowledge) — design-level visual communication, from deep research (topic 8). **Six findings verified 3-0** against Valve's TF2 primary sources (GDC 2008 *Stylization With a Purpose*; NPAR 2007 *Illustrative Rendering*): readability as a first-class engineerable objective, silhouette-first validation gate, value/contrast as eye-direction, reserve color for the single most urgent decision, photoreal detail fights readability, and a named style bible as convergence target. Signal-color case studies (BotW, Mirror's Edge, Naughty Dog / "yellow paint") are sourced-but-unverified (⚠️). Converts each rule into a per-asset **read contract** + silhouette-uniqueness gate for generators.
- `ai-authored-content-coherence` (knowledge) — the crux skill for headless / AI-authored games, from deep research (topic 9). Covers the oatmeal problem (Compton), two quality bars (differentiation vs. uniqueness), single voice via a curated corpus (Caves of Qud), generate-then-rationalize causation, recurring thematic **domains** that turn events into arcs, apophenia by design, a machine-readable **never-violate** lore bible with time-bounded atomic facts, static-backbone + procedural-tissue, the explicit **self-review pass** (= `procgen-review`), and finite-rarity ("Bach faucet" → Valenfeld's finite legendaries). **Heavily flagged:** this topic's verification phase largely failed (session limit) and synthesis partly failed, so nearly all rules are sourced-but-unverified (⚠️) — strong, well-cited hypotheses to validate, not settled law.

### Changed
- Normalized cross-references: `ai-authored-content` → `ai-authored-content-coherence` in `procedural-generation`, `game-design-process` (SKILL.md + PIPELINE.md); `open-world-design` GUIDE now links the live `art-direction-and-readability` skill instead of "the planned art-direction skill". The `narrative-and-quest-design` forward-links from `combat-design`, `systemic-emergent-design`, `game-design-fundamentals`, and `open-world-design` now resolve.

### Note
- These three skills were generated by the `deep-research` workflow over `docs/research-prompts.md` topics 7–9. The runs hit an account session limit during the adversarial-verification phase, so verification is incomplete for topics 7 and 9 (topic 8's core verified cleanly). Verification status is tagged inline in each GUIDE/CHECKLIST (✅ verified / ⚠️ sourced-unverified / ❌ refuted). A clean re-verification pass is the recommended follow-up.

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
