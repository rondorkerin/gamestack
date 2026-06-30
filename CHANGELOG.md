# Changelog

All notable changes to this skill pack are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this
project adheres to [Semantic Versioning](https://semver.org/).

## [0.11.2] — 2026-06-30

### Added — `iteration-loop`: human checkpoint before scaling a visual change

- `LOOP.md` gains a new §6 ("Human checkpoint before scaling a visual change"), between the external-request escalation (§5) and the practical run-through (now §7): the agent that generates a visual/aesthetic delta is not a reliable judge of whether it actually looks good, so Generate/Implement now scopes to **one instance first**, batches the plan (what's changing, how many instances, which reference), and requires an explicit human go/no-go before batch-applying to the rest.
- `SKILL.md`'s loop summary (steps 4–5) and frontmatter description/triggers updated to match.
- Surfaced by a real incident: an AI-driven iteration-loop pass on a biome generated low-poly tree replacements, self-certified its own Verify step, and applied the change across every tree in the biome before a human saw it — visibly worse than what it replaced. The fix is scope control (one instance → human review → batch), not better self-judgment, since the latter isn't achievable by the same agent that produced the output.

## [0.11.1] — 2026-06-30

### Fixed — bible-init adoption mode for existing projects

- `shared/PREAMBLE.md` §1 previously had exactly one branch for "bible missing": create empty stub files. Any project that already had an established design canon elsewhere (a `CLAUDE.md`, a `docs/` tree, a lore graph) would get that canon summarized into a second, competing source of truth the first time a gamestack process skill ran — a direct contradiction of `ETHOS.md` rule 5 ("keep design decisions in the bible").
- The preamble now checks for an existing design source of truth before stubbing the bible. If found, it asks once and records the answer in `./.gamestack/bible/sources.md`. **Pointer mode** keeps the existing bible filenames (`pillars.md`, `world.md`, `systems.md`, `lore.md`, `constraints.md` — unchanged, since all four engine overlays key off them) but each becomes a single-line redirect to the real source instead of a restated copy; a redirect can list more than one path, so it flexes to projects whose canon spans many files without forcing a merge-down. `decisions.md` is unaffected — it's gamestack's own log of what it decided, which exists nowhere else, so it always accumulates real content in both modes.
- `game-design-process/SKILL.md`'s first-touch routing table gains the missing fifth branch: an existing project with canon elsewhere routes to adoption detection instead of re-running Phase 1 Concept. `PIPELINE.md` Phase 1 cross-references the same skip.
- Surfaced by real dogfooding feedback: an agentic framework for AI-driven game dev disproportionately attracts users who already have months of Claude-authored canon, not greenfield projects — there was no onboarding path for that persona that didn't fork the truth.

## [0.11.0] — 2026-06-30

### Added — `plugin-update`: a self-check/update skill

- New utility skill `plugin-update` (`/gamestack:plugin-update`): locates the installed `gamestack@gamestack` plugin, refreshes the marketplace catalog, runs `claude plugin update` against the correct install scope, and surfaces the relevant `CHANGELOG.md` entries when it updates.
- Prompted by the fact that Claude Code's plugin auto-update is a client-side, per-user toggle that defaults to **off** for third-party marketplaces — there's no field in `marketplace.json`/`plugin.json` a marketplace author can set to turn it on for installers. This skill is the manual equivalent of a successful auto-update pass, runnable on demand instead of requiring every user to find the `/plugin` → Marketplaces → Enable auto-update toggle.
- Deliberately skips the shared `PREAMBLE.md`/`ETHOS.md` auto-load that every other process skill uses: it's pure plugin-maintenance tooling, not a design-bible operation, so loading the design ethos and engine-detection steps would be pure overhead.
- README's Install section now points to it alongside the existing `/plugin marketplace update gamestack` instructions, and explains the auto-update toggle is the user's call, not this repo's.

## [0.10.1] — 2026-06-30

### Added — `docs/headless-architecture.md`: a concrete worked example

- New doc giving the input→verb→seam architecture, the test-bench pattern, deterministic headless time (Godot's `--fixed-fps`), a playtest API, and a GitHub Actions CI example as real, runnable GDScript — the concrete instantiation of `iteration-loop`'s preview-harness (`LOOP.md` §2) and AI-playtester (`LOOP.md` §4) patterns, which were previously documented only at the engine-agnostic principle level.
- Prompted by public feedback that linking this repo from a post about a specific testable-architecture pattern was confusing, since the repo is a skill pack (design knowledge for an AI agent) and had no concrete reference implementation of that pattern anywhere in it. Addressed directly rather than just clarified away.

### Changed

- README gains a prominent "What this actually is" callout up top (this is a skill pack for an AI coding agent, not a game/engine/codebase) and a condensed "Headless, testable architecture" section pointing to the new doc.
- `iteration-loop/LOOP.md` §2 and §4 cross-reference the new doc as their concrete worked example.

## [0.10.0] — 2026-06-30

### Added — pair with BMad Game Dev Studio (BMGD)

- **Added [BMad Game Dev Studio](https://github.com/bmad-code-org/bmad-module-game-dev-studio) to `marketplace.json`** as a complementary production/PM pack rather than absorbing its content — gamestack stays the cited design/technical-craft knowledge brain that stops at spec; BMGD is the agent-driven epic/story/sprint production cycle (game-architect / game-designer / game-dev / solo-dev / tech-writer agents) gamestack has never had.
- **New `docs/architecture.md` section "Relationship to other frameworks: pair, don't merge"**, establishing the cross-reference pattern for future framework pairings: link to the external tool from the README, the architecture doc, and the one concrete skill that hands off — don't rebuild PM scaffolding inside gamestack.
- **Concrete hand-off**: `iteration-loop`'s human-playtester channel (`LOOP.md` §4) now points to BMGD's `gds-playtest-plan` (structured session design: objectives, observation guides, note-taking, post-session analysis) and its `gametest` workflow group (automated/perf testing) instead of reinventing session design. `game-design-process` notes the same pairing for tracked production once a spec is ready to implement.
- Identified gap worth naming: BMGD's `gametest` suite is human-playtest-session and automated-test focused — it has no equivalent to gamestack's AI-playtester-via-playtester-API concept or its procedural/AI-authored-content discipline (oatmeal test, fidelity loop). The pairing is asymmetric and both directions are documented.

### Changed

- README gains a "Production & PM pack" section (parallel to "Engine packs") with a gamestack-vs-BMGD comparison table. `plugin.json` + `marketplace.json` bumped to 0.10.0.

## [0.9.0] — 2026-06-30

### Added — `iteration-loop`: a generic, multimodal fidelity loop

- **New process skill `iteration-loop`**: the generic **Reference → Diff → Prioritize → Generate/Implement → Verify** loop that brings any system up to fidelity with its intended target, in any engine. One loop shape, instantiated per modality — visual (terrain/biome/dungeon/town/castle/character/spell-fx, against concept art), systemic (balance/economy/difficulty, against a stated target curve, via `difficulty-and-balancing`'s simulation method), and narrative (lore/quests, against the canon's never-violate fact set, via `worldbuilding-and-lore` / `ai-authored-content-coherence`). Gates *fidelity to a target*, an independent axis from `procgen-review`'s *variety across instances* — run both.
- **The preview-harness pattern** (`LOOP.md` §2): any visual system needs a rig that boots in isolation and captures deterministically on command, so Diff has something stable to compare.
- **The animation contact-sheet requirement** (`LOOP.md` §3): a single screenshot can't show motion. Animated subjects get a multi-frame strip (start/loop-mid/end, or first/mid/last for one-shots) captured as one flipbook-style image, turning "can't eyeball animation" into "read a 3-panel strip."
- **Two playtester channels** (`LOOP.md` §4): human playtesters for genuine fun/feel judgment, and an **AI playtester** that self-verifies every cycle via a "playtester API" — programmatic access to its own logs, game state, and screenshot output (plus input injection where available). Escalate to a human when the read is ambiguous or the question is inherently subjective, not just because human playtesting is slower to arrange.
- **External request escalation** (`LOOP.md` §5): when generation is the wrong tool for an asset/content gap (a hand-authored hero asset, a licensed line, a pixel-matched reference), log a structured request to a human-curated backlog instead of forcing a worse generated result.
- This fills the `playtest analysis` roadmap gap from earlier rounds, and supersedes it — `iteration-loop` covers more than analysis: the full loop that closes the gap, with both inspection and playtesting as its two verification channels.

### Changed

- `game-design-process` Phase 3 (Content) now runs `iteration-loop` per-system as content is built; Phase 5 (Playtest & iterate) is the same loop's playtesting channel run at whole-game scale.
- `procgen-review` cross-references `iteration-loop` as the fidelity-axis sibling to its variety-axis gate.
- `docs/architecture.md` Process tier list and packaging diagram updated; README process table and roadmap updated. `plugin.json` + `marketplace.json` bumped to 0.9.0.

## [0.8.0] — 2026-06-30

### Added — the technical-craft tier (rendering, shading, motion, generated geometry)

- **A fourth tier alongside universal craft, genre lenses, and technique modules** (`docs/architecture.md`): **technical craft** — the rendering/motion/geometry disciplines every real-time visual game obeys, regardless of genre. Stays engine-agnostic and design-spec level (principles, citations, "Test for:" criteria — no engine code), the same register as the rest of the pack, but technical-art rather than player-experience focused. Topic breadth inspired by [GodotPrompter](https://github.com/jame581/GodotPrompter) (the 51-skill Godot 4.x implementation library already curated as this pack's `godot` engine hand), researched and written independently.
- **Four new technical-craft knowledge skills**, each grown from a dedicated deep-research doc (`docs/research/round3-technical/T1–T4`, adversarially verified with inline ✅/⚠️/❌ tags):
  - `3d-graphics-and-rendering` — the rasterization pipeline, PBR vs. stylized/NPR materials, forward/deferred/forward+ lighting architecture, GI and shadow techniques, culling/LOD/draw-call/overdraw performance budgets, and the post-processing chain, framed as a frozen "imaging contract" a generator must not drift per scene.
  - `shaders-and-vfx` — the vertex/fragment/compute shader model, node-graph/procedural material authoring, the VFX toolkit (particles, trails, decals, distortion, screen-space effects) as the rendering substrate for `game-feel-and-juice`'s feedback channels, stylization techniques, and shader/overdraw performance failure modes.
  - `animation-systems` — skeletal fundamentals (skinning, FK/IK), blend trees/state machines/additive layers, procedural & physics-driven motion (active ragdoll, foot IK, motion matching), the animation cost model, and the system architecture that delivers `game-feel-and-juice`'s timing principles.
  - `procedural-geometry` — noise as a geometry substrate, terrain generation (heightfield vs. voxel/SDF, marching cubes, erosion, LOD/chunking), structural generation (L-systems, shape grammars, wave function collapse), mesh topology correctness, and vegetation/scatter — the geometry engine underneath `level-design` and `open-world-design`. Distinct from the existing `procedural-generation` skill (content/narrative coherence, not geometry math).
- **Round-3 research prompts** (`docs/research-prompts.md`): a new genre-agnostic `[CONTEXT-TECHNICAL]` block plus four `[TOPIC]` blocks that produced the skills above.

### Changed

- `game-design-process` orchestrator now pulls the technical-craft tier when a game has real-time 3D (or 2D-with-lighting) rendering, alongside the existing universal/genre/technique selection.
- README knowledge table gains the technical-craft tier; roadmap re-cut (genre breadth moves to round 4). `plugin.json` + `marketplace.json` bumped to 0.8.0.

## [0.7.0] — 2026-06-30

### Added — the universal-craft tier (general game dev, not just open-world RPG)

- **Re-architected the design brain into three tiers** (`docs/architecture.md`): **universal craft** (every game), **genre lenses** (that kind of game), **technique modules** (that technique). Diagnosis: the pack was a deep brain for *one* game (a procgen open-world RPG) — only 2 of 11 knowledge skills were genuinely universal, and the whole universal-craft layer (feel, teaching, UX, balancing, pacing, level design) was missing. Decision (with the user): ship as **layered marketplace plugins** — `gamestack-core` (universal + process spine, always installed) · genre packs (`gamestack-rpg`, `gamestack-action`, …) · technique packs (`gamestack-procgen`, …) · the existing engine hands. Core never hard-depends on a genre pack; genre packs depend on core, never sideways. The physical repo split is deferred and made safe by tier-tagging each `SKILL.md`.
- **Six new universal-craft knowledge skills**, each grown from a dedicated deep-research doc (`docs/research/round1-universal/U1–U6`, ~2,700 lines, adversarially verified with inline ✅/⚠️/❌ tags and ~118 machine-checkable "Test for:" criteria):
  - `game-feel-and-juice` — Swink's three building blocks (fix feel before you juice it), input latency & forgiveness (coyote time, buffering — Celeste's 7 systems), the juice toolkit + the **inverted-U feedback ceiling** (Kao 2020, N=3,018), the 12 animation principles (with the enemy/player anticipation inversion), camera & UI feel, and a per-action feedback budget for generators. **Extracted** as the universal foundation out of `combat-design`.
  - `level-design` — guiding the player (leading lines, landmarks, light, affordances), structure & gating (lock-and-key, interconnection), teaching through space (the antepiece), arena design, in-level pacing, the greybox/metrics process, and a beats-before-geometry level grammar. (Sources: Valve Cabal, Boss Keys, GDC Level Design Workshop, The Level Design Book, LaunchPad PCG.)
  - `onboarding-and-teaching` — teach one mechanic at a time (introduce→test→combine→twist), show-don't-tell over pop-ups, progressive disclosure / loops & arcs, the FTUE & retention funnel, and a dependency-ordered teaching ramp for a generated verb set. (SMB 1-1, Portal, Daniel Cook, kishōtenketsu, Hodent.)
  - `ui-ux-and-feedback` — the diegetic/non-diegetic/spatial/meta framework, information hierarchy & cognitive load (tiered HUD), game-state feedback, menu & navigation flow, and generating a HUD/UX spec from a mechanic set. (Fagerholt & Lorentzon, Dead Space, Hodent, Norman.)
  - `difficulty-and-balancing` — the most machine-checkable discipline: eliminating dominant strategies (payoff-matrix dominance checks, σ-thresholds), cost-curve/power-budget balance, difficulty curves & DDA/hidden directors (the transparency test), per-axis accessibility assists (Celeste), and simulation/self-play tuning. (Sirlin, Rosewater, L4D AI Director, RE4.)
  - `pacing-and-the-player-journey` — the **fractal interest curve** (encounter→level→session→playthrough at once), challenge/rest rhythm, novelty cadence, the nested engagement loops + a testable retention-ethics line, and a spec-level **pacing director** (generalizing the L4D 4-phase cycle + Warframe spawn manager). (Schell, Jenova Chen, Valve, L4D.)
- **Round-2+ research prompts** (`docs/research-prompts.md`): a new genre-agnostic `[CONTEXT-UNIVERSAL]` block (replacing the Valenfeld-anchored one) plus the six universal `[TOPIC]` blocks that produced the skills above.

### Changed

- `combat-design` re-pointed as the **combat application** of `game-feel-and-juice`: its old Sub-domain 1 (general juice theory) is condensed to a combat-specific slice (the feedback budget applied to hits; distinct signatures for hit/crit/block/parry/kill; hit-stop as the load-bearing primitive) that cites *up* to the new universal skill, with no duplicated theory. SKILL/GUIDE/CHECKLIST and the central law updated.
- `game-design-process` is now **genre-aware**: a new "classify the genre, then pull the right lens" step; Phase 2 becomes *"build the genre's core"* (universal spine always in play, genre lens selected) instead of *"build a world."*
- README knowledge table reorganized into the three tiers with the six new universal skills; roadmap re-cut into universal (round 2: audio, accessibility, playtesting, monetization, scope) / genre (round 3) / technique tracks. `plugin.json` + `marketplace.json` bumped to 0.7.0 with tiered descriptions.

## [0.6.0] — 2026-06-28

### Added
- **Architected the framework like gstack (workflow-spine phase).** From a `/plan-ceo-review` pass (SELECTIVE EXPANSION) that survived two rounds of adversarial spec review (4/10 → 8/10). Copies gstack's *shape*, not its plumbing:
  - `ETHOS.md` — the gamestack builder ethos (systems over content, no 10,000 bowls of oatmeal, hand-author the spine, gate before commit, design is engine-independent, interesting decisions, steal the fun, designer sovereignty). Loaded into every process skill.
  - `shared/PREAMBLE.md` — a shared preamble injected at the top of each process skill via the docs-verified `` !`cat ${CLAUDE_PLUGIN_ROOT}/...` `` load-time mechanism (single source, no build step — this is cheaper than gstack's `SKILL.md.tmpl` generator, which gamestack therefore does not need). It loads the design bible, detects the engine + overlay, states the completeness principle, and declares the completion-status protocol. Falls back to a Read instruction when `disableSkillShellExecution` is set.
  - **The design bible** (`<project>/.gamestack/bible/`: `pillars/world/systems/lore/constraints.md`, `corpus/`, `engine`, `decisions.md`) — gamestack's persistent cross-session memory and the engine-handoff contract. The gbrain analog.
  - `shared/GATE.md` — the headless generate→review→repair loop contract: `procgen-review` emits its verdict as the final fenced ```json block; an external `claude -p` harness (bounded retries) interprets pass/fail. Exit code belongs to the harness, not the skill.
  - Per-engine overlays `overlays/{godot,unity,unreal,threejs}.md` — gstack's model-overlay pattern applied to engines; each maps design specs to the engine pack's skills (Unity-general and Three.js flagged as gaps).
- `worldbuilding-and-lore` (knowledge) — the world bible and lore *delivery*, from deep research (topic 6). Five sub-domains: the **iceberg principle** (Hemingway's theory of omission — build the whole world as frozen, typed, queryable canon, surface ~10–12%; tag gaps `INTENTIONALLY_OMITTED`/`HIDDEN_BUT_KNOWABLE`, never `UNDECIDED`); **environmental & item-description storytelling** via Jenkins's four modes (evocative/embedded/enacted/emergent), with item text as the highest-leverage, most procedurally-friendly lore channel; an enumerable **"alien but coherent" identity** (Morrowind/Kirkbride) as a defense against generation drift toward generic-fantasy defaults; **deep history as faction-proxy pantheons** grown macro→micro (Jemisin's "Element X"), every "ancient" claim leaving a present-day trace; and **ludonarrative harmony** (Hocking) — every mechanic gets a (ludic, narrative) contract pair and links to canon. The AI-native program — frozen canon, generate-leaves-not-roots, validators-before-generators, contradiction-checking as a build gate — is flagged inline as engineering hypothesis, not received practice. Cross-links `narrative-and-quest-design`, `ai-authored-content-coherence`, `art-direction-and-readability`, `rpg-systems`, `permadeath-and-lethality`, `systemic-emergent-design`, `procgen-review`.
- `permadeath-and-lethality` (knowledge) — high-lethality / permadeath / single-save + meta-progression, from deep research (topic 5). Four areas: **fairness** (Meier/Spelunky/XCOM — death is always the player's fault, telegraphed, readable; the hand-authored fairness/readability backbone + an automated survivability pass as a first-class generation constraint); **mitigations** (short runs, Hades' opt-in stacking God Mode 20%→80%, deterministic per-subsystem RNG against save-scumming, earned escalation, every-death-teaches); **meta-progression & diegetic death** (the roguelike↔roguelite axis defined by persistence, skill staying load-bearing, Hades canonizing death into the fiction); and **Area 4 — single-save permadeath in a long-form open world**, treated as the genuinely-novel, **playtest-required** open question (character-mortal/world-persistent, succession frames from Crusader Kings / Dwarf Fortress / Rogue Legacy / NetHack, finite-loot conservation audits, opt-in-by-default). The source's two **⚠️ HEAVY FLAGS** on Area 4 are preserved verbatim. Cross-links `combat-design`, `rpg-systems`, `worldbuilding-and-lore`, `game-design-fundamentals`, `systemic-emergent-design`, the procgen skills.

### Changed
- Forward cross-links to `worldbuilding-and-lore` and `permadeath-and-lethality` now resolve — both slugs were already referenced by `game-design-process` (SKILL.md + PIPELINE.md), `narrative-and-quest-design`, `combat-design`, and `rpg-systems`. README Knowledge table gains both rows; the (stale) knowledge roadmap is refreshed to tick the skills already shipped (narrative, art-direction, AI-authored coherence) plus the two new ones, leaving only "Pacing & game feel" open.
- These two skills arrived from the `deep-research` workflow (topics 5–6) as **complete syntheses with their own Caveats / HEAVY-FLAG sections**, so — unlike topics 7/9 — they do not carry per-claim ✅/⚠️/❌ verification tags; each carries the source caveats inline. **Permadeath Area 4 (single-save long-form open world) is explicitly extrapolation requiring playtests; worldbuilding's AI-authoring mitigations are flagged as engineering hypotheses.**
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
