# 🎮 gamestack

**An agentic framework for building games on any platform — for [Claude](https://claude.com/claude-code). Like gstack is for software, but for games.**

gamestack is two things working together:

- **A design brain** — first-party, engine-agnostic skills that give Claude the *knowledge* of game design (cited, discipline by discipline) and the *process* to apply it: concept → world → content → self-review.
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

## First-party skills (the design brain)

### Process
| Skill | What it does | Status |
|-------|--------------|--------|
| [`game-design-process`](plugins/gamestack/skills/game-design-process) | The orchestrator. Walks concept → world → content → review → iterate and names which skill to use at each phase. The entry point for "I'm designing a game." | 🟢 v0.1 |
| [`engine-router`](plugins/gamestack/skills/engine-router) | The platform router. Takes a ready design and routes implementation to the right engine pack (Godot/Unreal/Unity), keeping design logic and engine code cleanly separated. | 🟢 v0.1 |
| [`procgen-review`](plugins/gamestack/skills/procgen-review) | The self-review pass for generated content. Runs the oatmeal test, the fanfic/retell test, a cross-instance sameness scan, and the intentionality + anti-pattern gates. Built to run in a headless generation loop. | 🟢 v0.1 |

### Knowledge
| Skill | What it covers | Status |
|-------|----------------|--------|
| [`game-design-fundamentals`](plugins/gamestack/skills/game-design-fundamentals) | The spine the rest of the pack builds on: interesting decisions (Meier), flow & difficulty curves, motivation (Self-Determination Theory), feedback & agency, and choice architecture — with machine-checkable tests for a generation loop. | 🟢 v0.1 |
| [`open-world-design`](plugins/gamestack/skills/open-world-design) | World structure (the triangle rule, gravity, interconnection, biomes, verticality), diegetic navigation, signal color, exploration pull, and spatial pacing. | 🟢 v0.2 |
| [`procedural-generation`](plugins/gamestack/skills/procedural-generation) | Beating the "10,000 bowls of oatmeal" problem: perceptual uniqueness, the handcrafted-anchor + constrained-fill hybrid, voice-consistent corpora, multiplicative systems, intentionality. | 🟢 v0.1 |
| [`combat-design`](plugins/gamestack/skills/combat-design) | Combat & game feel: juice tuned to the Medium–High band (the inverted-U), hit-stop & the impact bundle, telegraphing as fairness, danger-cue vocabularies, enemy silhouettes & role-based rosters, readable multi-enemy chaos (aggression tokens), and the Souls-like commitment/stamina/checkpoint loop. | 🟢 v0.1 |
| [`rpg-systems`](plugins/gamestack/skills/rpg-systems) | The RPG number-systems triad: progression (price advancement, zone level-bands not world-scaling), economy (faucets/sinks, desirable sinks, anti-hoarding under scarcity, currency-as-material), and loot (rarity baseline, build-defining affixes over stat-sticks, procedural breadth vs. hand-authored identity, the finite-legendary manifest). | 🟢 v0.1 |
| [`systemic-emergent-design`](plugins/gamestack/skills/systemic-emergent-design) | Authoring affordances not solutions: the immersive-sim substrate (universal rules, intention & perceivable consequence), emergence from few deep interacting systems, multiplicative vs. additive design (the chemistry engine), the "good GM" analogy, and making procgen cohere instead of becoming oatmeal. | 🟢 v0.1 |
| [`narrative-and-quest-design`](plugins/gamestack/skills/narrative-and-quest-design) | Quests, reactivity, and factions: the facts-database reactivity substrate (Witcher 3), planner-over-world-state procedural quests, blurring flavor vs. consequence, the no-fetch-quest doctrine, radiant-as-supplement, and faction allegiance dilemmas. | 🟢 v0.2 |
| [`worldbuilding-and-lore`](plugins/gamestack/skills/worldbuilding-and-lore) | The world bible and lore *delivery*: the iceberg principle (build the whole, ship ~10% as frozen typed canon), environmental & item-description storytelling (Jenkins's four modes), an enumerable "alien but coherent" identity that resists generation drift, deep history as faction-proxy pantheons, and ludonarrative harmony (every mechanic answers a lore question). | 🟢 v0.1 |
| [`permadeath-and-lethality`](plugins/gamestack/skills/permadeath-and-lethality) | High-lethality / permadeath / single-save: the fairness & readability backbone (death is always the player's fault), mitigations that cut failure cost without removing stakes (short runs, opt-in stacking assist, deterministic anti-save-scum), meta-progression & diegetic death (Hades), and the flagged-novel problem of single-save permadeath in a long-form open world. | 🟢 v0.1 |
| [`art-direction-and-readability`](plugins/gamestack/skills/art-direction-and-readability) | Design-level visual communication: readability as an engineerable objective, silhouette-first validation, value/contrast eye-direction, reserved signal colors, fidelity-fights-readability, the named style bible, and per-asset read contracts for generators. | 🟢 v0.1 |
| [`ai-authored-content-coherence`](plugins/gamestack/skills/ai-authored-content-coherence) | The crux skill: keeping AI-authored content coherent at scale — the oatmeal problem, single voice via curated corpus, generate-then-rationalize causation, recurring thematic domains, a machine-readable never-violate lore bible, and the self-review pass. | 🟢 v0.2 |

> ✅ `narrative-and-quest-design` and `ai-authored-content-coherence` have now completed the verification pass that was previously cut short: a clean adversarial round re-checked every rule against primary sources. All rules are confirmed (a handful carried quote/attribution/mechanism corrections, applied and noted inline); `narrative-and-quest-design` §6 keeps two ❌ refuted claims as do-not-encode. The one caveat that remains for AI-authored coherence is empirical, not citational — coherence at full open-world scale is unsolved, so de-risk with the oatmeal/fanfic tests before scaling. `art-direction-and-readability` remains verification-confirmed at its core (TF2 primary sources).

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

**Design brain — knowledge:**
- ✅ Game-design fundamentals · ✅ Open-world design · ✅ Procedural generation · ✅ Combat & game feel
- ✅ RPG systems (progression, economy, loot/itemization)
- ✅ Systemic / emergent design (immersive sim, multiplicative systems)
- ✅ Permadeath & lethality · ✅ Worldbuilding & lore · ✅ Narrative & quest design
- ✅ Art direction & readability · ✅ AI-authored content coherence · ⬜ Pacing & game feel

**Design brain — process:**
- ✅ Game-design process (orchestrator) · ✅ Engine router · ✅ Procgen review
- ⬜ Concept & pillars planning · ⬜ World-layout pass · ⬜ Quest quality gate
- ⬜ Lore-coherence audit · ⬜ Design review · ⬜ Playtest analysis

**Production & tooling:**
- ⬜ Performance optimization (engine-agnostic principles + per-engine hooks)
- ⬜ Asset pipelines via MCP (e.g. Meshy / 3D generation, texture pipelines)
- ⬜ Algorithms (pathfinding, spatial partitioning, noise) · ⬜ Production & project management

---

## Contributing

Research, citations, production lessons, and **engine packs to slot in** are all welcome — see **[CONTRIBUTING.md](CONTRIBUTING.md)** for the skill template and conventions. The design brain's knowledge skills are grown from deep-research docs; reusable prompts live in **[docs/research-prompts.md](docs/research-prompts.md)**.

## License

[MIT](LICENSE) © Arcos Labs. Engine packs are MIT-licensed by their respective authors. Use it, fork it, ship games with it.
