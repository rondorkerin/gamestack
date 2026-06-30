# 🎮 gamestack

**An agentic framework for building games on any platform — for [Claude](https://claude.com/claude-code). Like gstack is for software, but for games.**

[![Discord](https://img.shields.io/badge/Discord-Join-5865F2?logo=discord&logoColor=white)](https://discord.gg/xRMGEcH6Xg) [![X (Twitter)](https://img.shields.io/badge/X-%40metatransformr-000000?logo=x)](https://x.com/metatransformr)

Questions, feedback, or just want to follow along: [Discord](https://discord.gg/xRMGEcH6Xg) · [@metatransformr on X](https://x.com/metatransformr)

### What this actually is (read this first if you came from a link)

This is a **skill pack for an AI coding agent** — cited game-design knowledge plus reusable
engineering patterns, written as Markdown that Claude Code loads on demand. It is **not** a
game, not a game engine, and not a single codebase you clone and `godot --editor` into — there's
no `.tscn` file in this repo. If you're looking for the actual input/verb/seam architecture and
headless test-bench pattern referenced elsewhere (e.g. a Reddit post about building a Godot RPG
from the terminal), the concrete, runnable-code version of that lives in
**[`docs/headless-architecture.md`](docs/headless-architecture.md)** — a worked Godot example,
with the input→verb→seam pattern, the bench pattern, deterministic headless time
(`--fixed-fps`), a playtest API, and a GitHub Actions example. Everything else in this README
is the design-knowledge half: what an agent should *know* before and while it builds.

gamestack is two things working together:

- **A design brain** — first-party, engine-agnostic skills that give Claude the *knowledge* of game design (cited, discipline by discipline) and the *process* to apply it: concept → world → content → self-review → iterate.
- **Engine hands** — curated, best-in-class community skill packs for each engine (Godot, Unreal, Unity), referenced directly from one marketplace so Claude can turn a design into real engine code.

You design once, then hand off to whichever engine you're shipping in. The design layer doesn't overlap the engine packs — it's the layer they're missing.

```
                        gamestack  (one marketplace)
                              │
        ┌─────────────────────┼──────────────────┬──────────────────┐
   gamestack                godot               unreal           unity-jahro
 (first-party brain)       (jame581)          (quodsoler)       (jahro-console)
 design · procgen ── implements ──▶  GDSCRIPT · C++ · per-engine code
 combat · process
```

We don't reinvent engine work others do well — we **slot it in** and add the design/discipline layer on top, plus the router that binds them.

---

## How it fits together

| Layer | What it is | Who builds it |
|-------|------------|---------------|
| 🧠 **Design brain** (first-party) | Cited design disciplines + the headless generate-and-review pipeline. Engine-agnostic. | This repo |
| 🎮 **Engine hands** (curated) | Per-engine implementation skills (code patterns, systems, optimization), referenced from the marketplace by their authors. | Community, MIT-licensed |
| 🔀 **The router** | `engine-router` — detects the target engine and routes: design → brain, implementation → the right engine pack. | This repo |

---

## The two first-party skill kinds

| Layer | What it is | Example |
|-------|------------|---------|
| 🧠 **Knowledge** | A cited design discipline as a guide + do/don't checklist. The "what good looks like." | `open-world-design`, `procedural-generation` |
| ⚙️ **Process** | A workflow verb the agent *runs* — generate, review, route, plan. Draws on the knowledge skills. | `procgen-review`, `game-design-process`, `engine-router` |

---

## The pipeline

```
  CONCEPT ──▶ WORLD & SYSTEMS ──▶ CONTENT ──▶ REVIEW & GATE ──▶ HANDOFF ──▶ ENGINE CODE
  pillars,    structure, nav,      procgen +   oatmeal/fanfic/    engine-     godot / unreal /
  core loop,  progression,         handcrafted  sameness/anti-     router      unity pack
  signature   economy, combat,     anchors,     pattern gates      picks the   implements
  mechanics   systemic rules       lore                            engine      the spec
  └──────────────── design brain (first-party) ───────────────┘ └──── engine hands ────┘
```

`game-design-process` orchestrates the design half and pulls the right skill at each phase. `engine-router` runs the handoff to engine code. Start at either, or invoke any skill directly.

---

## Headless, testable architecture (input → verb → seam)

The other half of how this actually gets *built* — not just designed — once content exists: every system needs to be checkable without a human walking to it in a running game, and every input needs to drive the game through one auditable path.

- **Input never performs logic.** A keyboard read, a gamepad read, and an automated test all translate to the same verb call on a seam (a single object that owns the action). Keyboard and gamepad can't drift apart; a headless test exercises the *real* code path.
- **Every system that's worth iterating on gets a preview harness ("bench")** — boots that one slice headless, takes parameters from the CLI, captures deterministically, quits. `godot --headless --path . res://tools/benches/DungeonBench.tscn -- --shots --seed=1234` and you get the same PNGs every time — golden-image testing for procgen, no editor required.
- **Headless time isn't wall-clock time by default** — Godot's `--fixed-fps N` flag forces every frame's `delta` to exactly `1/N` regardless of real elapsed time, making physics/animation/movement fully deterministic and machine-speed-independent.
- **A playtest API is just verbs composed into a session** (`boot()`, `goto()`, `fight_nearest()`, `loot_nearby()`, `rest()`) — an integration test, or an AI agent, can play the real game headless and read the result back from logs and screenshots.

This is the concrete form of `iteration-loop`'s **preview-harness pattern** and **AI-playtester** concept (the skill describes it engine-agnostically; the pattern itself is good testing hygiene regardless of whether an AI ever touches it). Full worked example with real GDScript, the determinism flag, and a GitHub Actions setup: **[`docs/headless-architecture.md`](docs/headless-architecture.md)**.

---

## First-party skills (the design brain)

### Process
| Skill | What it does | Status |
|-------|--------------|--------|
| [`game-design-process`](plugins/gamestack/skills/game-design-process) | The orchestrator. Walks concept → world → content → review → iterate and names which skill to use at each phase. The entry point for "I'm designing a game." | 🟢 v0.1 |
| [`engine-router`](plugins/gamestack/skills/engine-router) | The platform router. Takes a ready design and routes implementation to the right engine pack (Godot/Unreal/Unity), keeping design logic and engine code cleanly separated. | 🟢 v0.1 |
| [`procgen-review`](plugins/gamestack/skills/procgen-review) | The self-review pass for generated content. Runs the oatmeal test, the fanfic/retell test, a cross-instance sameness scan, and the intentionality + anti-pattern gates. Built to run in a headless generation loop. | 🟢 v0.1 |
| [`iteration-loop`](plugins/gamestack/skills/iteration-loop) | The generic Reference → Diff → Prioritize → Generate/Implement → Verify loop that brings any system — visual, systemic, or narrative — up to fidelity with its intended target, in any engine. Covers the preview-harness pattern, animation contact-sheet capture, the human + AI playtester channels (the latter via a "playtester API"), and the external-asset-request escalation. Gates *fidelity*; `procgen-review` gates *variety* — independent axes. | 🟢 v0.1 |

### Knowledge

Knowledge skills are organized into three **tiers** (see [`docs/architecture.md`](docs/architecture.md)): **universal craft** (every game needs them → headed for `gamestack-core`), **genre lenses** (pulled for *that kind* of game), and **technique modules** (pulled when *using* that technique). Today they all live in the one `gamestack` plugin and are tagged with their tier in `SKILL.md`; the split into separate marketplace packs is a later, mechanical move.

**🌐 Universal craft** — applies to every genre
| Skill | What it covers | Status |
|-------|----------------|--------|
| [`game-design-fundamentals`](plugins/gamestack/skills/game-design-fundamentals) | The spine the rest of the pack builds on: interesting decisions (Meier), flow & difficulty curves, motivation (Self-Determination Theory), feedback & agency, and choice architecture — with machine-checkable tests for a generation loop. | 🟢 v0.1 |
| [`game-feel-and-juice`](plugins/gamestack/skills/game-feel-and-juice) | The moment-to-moment feel of *any* action: Swink's three building blocks (fix feel before you juice it), input latency & forgiveness (coyote time, buffering), the juice toolkit + the inverted-U ceiling (Kao), the 12 animation principles, camera & UI feel — as a per-action feedback budget a generator can't exceed. (Extracted as the universal foundation under `combat-design`.) | 🟢 v0.1 |
| [`level-design`](plugins/gamestack/skills/level-design) | The craft of shaping space & encounters: guiding the player (leading lines, landmarks, light, affordances), structure & gating (lock-and-key, interconnection), teaching through space, arena design, in-level pacing, the greybox/metrics process — and a level-design *grammar* for generators. | 🟢 v0.1 |
| [`onboarding-and-teaching`](plugins/gamestack/skills/onboarding-and-teaching) | Tutorialization & the first-time experience: teach one mechanic at a time (introduce→test→combine→twist), show-don't-tell over pop-ups, progressive disclosure, the FTUE/retention funnel — and a dependency-ordered teaching ramp for a generated verb set. | 🟢 v0.1 |
| [`ui-ux-and-feedback`](plugins/gamestack/skills/ui-ux-and-feedback) | Game UI/UX at the design level: the diegetic/non-diegetic/spatial/meta framework, information hierarchy & cognitive load, game-state feedback, menu & navigation flow — and generating a HUD/UX spec from a mechanic set. | 🟢 v0.1 |
| [`difficulty-and-balancing`](plugins/gamestack/skills/difficulty-and-balancing) | The most machine-checkable discipline: eliminating dominant strategies (payoff-matrix dominance checks), cost-curve/power-budget balance, difficulty curves & DDA/hidden directors, per-axis accessibility assists, and metrics/simulation-driven tuning. | 🟢 v0.1 |
| [`pacing-and-the-player-journey`](plugins/gamestack/skills/pacing-and-the-player-journey) | Macro pacing as a complement to game feel: the fractal interest curve (encounter→level→session→playthrough), challenge/rest rhythm, novelty cadence, the nested engagement loops + retention-ethics line, and a spec-level **pacing director** for generated content. | 🟢 v0.1 |
| [`art-direction-and-readability`](plugins/gamestack/skills/art-direction-and-readability) | Design-level visual communication: readability as an engineerable objective, silhouette-first validation, value/contrast eye-direction, reserved signal colors, fidelity-fights-readability, the named style bible, and per-asset read contracts for generators. | 🟢 v0.1 |

**🛠️ Technical craft** — applies to every real-time 3D (or 2D-with-lighting) game, regardless of genre. Engine-agnostic design-spec level (principles, citations, "Test for:" criteria — no engine code); topic breadth inspired by [GodotPrompter](https://github.com/jame581/GodotPrompter)'s implementation-skill library, but researched and written independently. See [`docs/architecture.md`](docs/architecture.md#technical-craft-is-design-spec-not-implementation) for why this is a separate tier from universal craft and technique modules.
| Skill | What it covers | Status |
|-------|----------------|--------|
| [`3d-graphics-and-rendering`](plugins/gamestack/skills/3d-graphics-and-rendering) | The rendering pipeline spine: rasterization stages, PBR vs. stylized/NPR materials, the forward/deferred/forward+ lighting architecture, GI & shadow choices, culling/LOD/draw-call/overdraw performance budgets, and the post-processing chain — framed as a frozen "imaging contract" a generator must not drift per scene. | 🟢 v0.1 |
| [`shaders-and-vfx`](plugins/gamestack/skills/shaders-and-vfx) | The shading & particle layer: the vertex/fragment/compute shader model, node-graph/procedural material authoring, the VFX toolkit (particles, trails, decals, distortion, screen-space effects) as the rendering substrate for `game-feel-and-juice`'s feedback channels, stylization techniques, and shader/overdraw performance failure modes. | 🟢 v0.1 |
| [`animation-systems`](plugins/gamestack/skills/animation-systems) | Motion-system architecture: skeletal fundamentals (skinning, FK/IK), blend trees/state machines/additive layers, procedural & physics-driven motion (active ragdoll, foot IK, motion matching), the animation cost model (compression, bone budgets, root motion), and the system that delivers `game-feel-and-juice`'s timing principles. | 🟢 v0.1 |
| [`procedural-geometry`](plugins/gamestack/skills/procedural-geometry) | Generating *geometry* correctly and with variety: noise as a substrate (Perlin/Simplex/fBm, domain warping), terrain (heightfield vs. voxel/SDF, marching cubes, erosion, LOD), structural generation (L-systems, shape grammars, wave function collapse), mesh topology correctness (manifold/watertight/winding validators), and vegetation/scatter — the geometry engine underneath `level-design` and `open-world-design`. | 🟢 v0.1 |

**🎯 Genre lenses** — pulled for *that kind* of game
| Skill | What it covers | Status |
|-------|----------------|--------|
| [`open-world-design`](plugins/gamestack/skills/open-world-design) | World structure (the triangle rule, gravity, interconnection, biomes, verticality), diegetic navigation, signal color, exploration pull, and spatial pacing. | 🟢 v0.2 |
| [`combat-design`](plugins/gamestack/skills/combat-design) | Combat application of game feel: telegraphing as fairness, danger-cue vocabularies, enemy silhouettes & role-based rosters, readable multi-enemy chaos (aggression tokens), and the Souls-like commitment/stamina/checkpoint loop. (General feel → `game-feel-and-juice`.) | 🟢 v0.2 |
| [`rpg-systems`](plugins/gamestack/skills/rpg-systems) | The RPG number-systems triad: progression (price advancement, zone level-bands not world-scaling), economy (faucets/sinks, desirable sinks, anti-hoarding under scarcity, currency-as-material), and loot (rarity baseline, build-defining affixes over stat-sticks, procedural breadth vs. hand-authored identity, the finite-legendary manifest). | 🟢 v0.1 |
| [`narrative-and-quest-design`](plugins/gamestack/skills/narrative-and-quest-design) | Quests, reactivity, and factions: the facts-database reactivity substrate (Witcher 3), planner-over-world-state procedural quests, blurring flavor vs. consequence, the no-fetch-quest doctrine, radiant-as-supplement, and faction allegiance dilemmas. | 🟡 v0.1 ⚠️ |
| [`worldbuilding-and-lore`](plugins/gamestack/skills/worldbuilding-and-lore) | The world bible and lore *delivery*: the iceberg principle (build the whole, ship ~10% as frozen typed canon), environmental & item-description storytelling (Jenkins's four modes), an enumerable "alien but coherent" identity that resists generation drift, deep history as faction-proxy pantheons, and ludonarrative harmony (every mechanic answers a lore question). | 🟢 v0.1 |
| [`permadeath-and-lethality`](plugins/gamestack/skills/permadeath-and-lethality) | High-lethality / permadeath / single-save: the fairness & readability backbone (death is always the player's fault), mitigations that cut failure cost without removing stakes (short runs, opt-in stacking assist, deterministic anti-save-scum), meta-progression & diegetic death (Hades), and the flagged-novel problem of single-save permadeath in a long-form open world. | 🟢 v0.1 |

**🔧 Technique modules** — pulled when *using* that technique
| Skill | What it covers | Status |
|-------|----------------|--------|
| [`procedural-generation`](plugins/gamestack/skills/procedural-generation) | Beating the "10,000 bowls of oatmeal" problem: perceptual uniqueness, the handcrafted-anchor + constrained-fill hybrid, voice-consistent corpora, multiplicative systems, intentionality. | 🟢 v0.1 |
| [`ai-authored-content-coherence`](plugins/gamestack/skills/ai-authored-content-coherence) | The crux skill: keeping AI-authored content coherent at scale — the oatmeal problem, single voice via curated corpus, generate-then-rationalize causation, recurring thematic domains, a machine-readable never-violate lore bible, and the self-review pass. | 🟡 v0.1 ⚠️ |
| [`systemic-emergent-design`](plugins/gamestack/skills/systemic-emergent-design) | Authoring affordances not solutions: the immersive-sim substrate (universal rules, intention & perceivable consequence), emergence from few deep interacting systems, multiplicative vs. additive design (the chemistry engine), the "good GM" analogy, and making procgen cohere instead of becoming oatmeal. | 🟢 v0.1 |

> ⚠️ Some earlier skills were built from a deep-research pass whose adversarial-verification phase was cut short by a session limit. `art-direction-and-readability` is verification-confirmed at its core (TF2 primary sources); `narrative-and-quest-design` and `ai-authored-content-coherence` carry a mix of verified and **sourced-but-unverified** rules, tagged inline (✅/⚠️/❌). Re-run a clean verification pass before treating the ⚠️ rules as settled.

---

## Engine packs (the hands)

Curated from the community and referenced directly in the gamestack marketplace — install the one(s) for your engine. All MIT-licensed; credit to their authors.

| Pack | Engine | Covers | Source | Status |
|------|--------|--------|--------|--------|
| `godot` | Godot 4.x | GDScript patterns, systems (ability/inventory/dialogue/event-bus), optimization, testing, export pipeline | [jame581/GodotPrompter](https://github.com/jame581/GodotPrompter) ⭐334 | 🟢 strong |
| `unreal` | Unreal Engine | 27 C++ skills: gameplay framework, rendering, networking/replication, animation, Niagara, world streaming | [quodsoler/unreal-engine-skills](https://github.com/quodsoler/unreal-engine-skills) ⭐243 | 🟢 strong |
| `unity-jahro` | Unity | AI-assisted debugging: structured logging, runtime commands, variable watching (Jahro console) | [jahro-console/unity-agent-skills](https://github.com/jahro-console/unity-agent-skills) | 🟡 debug-only |
| Unity (general) | Unity | General C# authoring | — | ⬜ roadmap |
| Three.js / web | Three.js | Web/WebGL game implementation | — | ⬜ roadmap |

---

## Production & PM pack (pairs with gamestack, doesn't overlap it)

[**BMad Game Dev Studio (BMGD)**](https://github.com/bmad-code-org/bmad-module-game-dev-studio) ⭐197 (third-party, MIT, by BMad Code) is a sibling framework, not a competitor — it covers what gamestack deliberately doesn't:

| | gamestack | BMGD |
|---|---|---|
| **What it is** | Cited design/technical-craft knowledge brain | Agent-driven production process (PM layer) |
| **Output** | Specs, constraint sets, a design bible — engine-agnostic | GDD/PRD/architecture docs, epics, stories, sprint status, retrospectives |
| **Playtesting** | `iteration-loop`'s fidelity loop (reference→diff→verify) + an AI-playtester concept (self-play via a playtester API) | `gametest` workflow group: structured **human** playtest-session design (objectives, observation guides, note-taking, post-session analysis), automated test scaffolding, performance testing |
| **Procedural / AI-authored content** | A first-class concern (`procedural-generation`, `ai-authored-content-coherence`, the oatmeal test, hand-anchor + constrained-fill) | Not a focus — built for traditional sprint-driven production |
| **Stops at** | Spec/design-bible, hands off via `engine-router` | Nothing — it carries through to tracked implementation stories |

Install both when you want gamestack's design depth *and* a tracked production cycle: `/plugin install bmad-game-dev-studio@gamestack`. The concrete pairing point is `iteration-loop`'s human-playtester channel, which points to BMGD's `gds-playtest-plan` for structured session design rather than reinventing it — see [`docs/architecture.md`](docs/architecture.md) for the full relationship.

---

## Install

### Marketplace (recommended)

In Claude Code, add the marketplace once:

```
/plugin marketplace add rondorkerin/gamestack
```

Then install the design brain plus your engine pack(s):

```
/plugin install gamestack          # the design brain (always)
/plugin install godot@gamestack    # + your engine
/plugin install unreal@gamestack
/plugin install unity-jahro@gamestack
```

Confirm with `/plugin`. Asking Claude a design question (e.g. *"walk me through designing a procedural open-world RPG in Godot"*) should pull in `game-design-process`, and `engine-router` handles the handoff to the Godot pack. Pull updates with:

```
/plugin marketplace update gamestack
```

### Manual (single skill, no plugin)

First-party skills are plain folders — clone and copy the ones you want:

```bash
git clone https://github.com/rondorkerin/gamestack.git
cp -r gamestack/plugins/gamestack/skills/open-world-design ~/.claude/skills/   # all projects
# or into <your-project>/.claude/skills/                                        # one project only
```

Claude auto-discovers skills in those directories on the next session — verify with `/skills`.

### Headless / Agent SDK

The skills work under `claude -p` and the Agent SDK. With the plugins installed, point a headless run at the pipeline:

```bash
claude -p "Use the game-design-process skill to design a procedural open-world RPG in Godot. \
Run procgen-review on all generated content, then use engine-router to implement it with the godot pack."
```

`game-design-process` sequences concept → world → content → review, `procgen-review` gates generated content, and `engine-router` hands the spec to the engine pack — no human in the loop.

---

## Using it

Just describe what you're doing:

> "I'm starting a procedural open-world RPG in Godot. Walk me through it." → `game-design-process`

> "Lay out the macro-map for the first region." → `open-world-design`

> "I generated 50 dungeons — check them for sameness before I commit them." → `procgen-review`

> "The design's done — now build it in Unreal." → `engine-router` → `unreal` pack

Or invoke a skill directly with `/<skill-name>`.

---

## Skill anatomy

```
skills/<skill-name>/
├── SKILL.md       # frontmatter (name + "use when" trigger) and overview
├── GUIDE.md       # knowledge skills: the cited "why"
├── CHECKLIST.md   # knowledge skills: actionable do/don't + test-for criteria
└── *.md           # process skills: the procedure the agent runs
```

`SKILL.md` is always cheap; everything else loads on demand. See [`docs/SKILL_TEMPLATE.md`](docs/SKILL_TEMPLATE.md).

### Framework internals

The process skills share a spine (modeled on gstack's shape):

```
plugins/gamestack/
├── ETHOS.md            # the builder ethos, injected into every process skill
├── shared/
│   ├── PREAMBLE.md     # injected at load time via !`cat ${CLAUDE_PLUGIN_ROOT}/...`
│   └── GATE.md         # the headless generate→review→repair loop contract
└── overlays/           # per-engine spec→pack-skill mapping (godot/unreal/unity/threejs)
```

Each process skill opens by injecting `PREAMBLE.md` + `ETHOS.md`, which loads the **design bible** — `<your-project>/.gamestack/bible/` — the persistent, cross-session record of your pillars, world, systems, lore, constraints, and decisions. It is also the engine-independent contract `engine-router` hands to the engine pack. Design once; the bible remembers; implement per engine.

---

## Roadmap

**Engine coverage** (curate strong community packs, or build first-party):
- ✅ Godot · ✅ Unreal · 🟡 Unity (debug-only — needs a general authoring pack) · ⬜ Three.js / web

**Design brain — knowledge — 🌐 universal craft** (the spine every genre needs):
- ✅ Game-design fundamentals · ✅ Game feel & juice · ✅ Level design · ✅ Onboarding & teaching
- ✅ UI/UX & feedback · ✅ Difficulty & balancing · ✅ Pacing & the player journey · ✅ Art direction & readability
- ⬜ Round 2: audio design · accessibility · playtesting & telemetry · monetization & business model · scope/production

**Design brain — knowledge — 🛠️ technical craft** (the rendering/motion/geometry spine every real-time visual game needs, design-spec level — see [`docs/architecture.md`](docs/architecture.md)):
- ✅ 3D graphics & rendering · ✅ Shaders & VFX · ✅ Animation systems · ✅ Procedural geometry

**Design brain — knowledge — 🎯 genre lenses** (more genres broaden general game-dev coverage):
- ✅ Open-world design · ✅ Combat design · ✅ RPG systems · ✅ Narrative & quest · ✅ Worldbuilding & lore · ✅ Permadeath & lethality
- ⬜ Round 4: platformer/movement · shooter · puzzle · strategy/tactics · sim/management · survival/crafting · deckbuilder · horror

**Design brain — knowledge — 🔧 technique modules:**
- ✅ Procedural generation · ✅ AI-authored content coherence · ✅ Systemic / emergent design · ⬜ Multiplayer/netcode · ⬜ Save/persistence

> Architecture & packaging (core / genre / technique multi-pack split, genre-aware orchestrator): see [`docs/architecture.md`](docs/architecture.md). Research prompts for the open rounds: [`docs/research-prompts.md`](docs/research-prompts.md).

**Design brain — process:**
- ✅ Game-design process (orchestrator) · ✅ Engine router · ✅ Procgen review · ✅ Iteration loop (covers playtest analysis, human + AI playtester)
- ⬜ Concept & pillars planning · ⬜ World-layout pass · ⬜ Quest quality gate
- ⬜ Lore-coherence audit · ⬜ Design review

**Production & tooling:**
- ⬜ Performance optimization (engine-agnostic principles + per-engine hooks)
- ⬜ Asset pipelines via MCP (e.g. Meshy / 3D generation, texture pipelines)
- ⬜ Algorithms (pathfinding, spatial partitioning, noise) · ⬜ Production & project management

---

## Contributing

Research, citations, production lessons, and **engine packs to slot in** are all welcome — see **[CONTRIBUTING.md](CONTRIBUTING.md)** for the skill template and conventions. The design brain's knowledge skills are grown from deep-research docs; reusable prompts live in **[docs/research-prompts.md](docs/research-prompts.md)**.

## Community

[Discord](https://discord.gg/xRMGEcH6Xg) for questions, feedback, and discussion · [@metatransformr](https://x.com/metatransformr) on X for updates.

## License

[MIT](LICENSE) © Arcos Labs. Engine packs are MIT-licensed by their respective authors. Use it, fork it, ship games with it.
