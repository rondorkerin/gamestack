# 🎮 gamedev-skills

**The game-design *process* for [Claude](https://claude.com/claude-code) — like gstack is for software engineering, but for designing games.**

Most "game design" resources are static reading. This is a working pipeline. Install it and Claude gains both the **knowledge** of game design (cited, discipline by discipline) and the **process** to apply it — a set of workflow skills that take a game from a one-line concept to a designed, generated, self-reviewed world.

It's built for how Claude actually ships games today: **headless, engine-agnostic (we use Godot), and procedural.** A headless agent can't hand-place ten thousand objects, but it *can* author systems, generate content from rules, and critique its own output against hard quality bars. So the pack leans into procedural / AI-authored design and gives the agent the review passes that keep generated content from collapsing into sameness.

It stops at the **design and spec level** — world bibles, system specs, content, quality gates. It does not write your gameplay code. That handoff is deliberate.

---

## Two kinds of skill

| Layer | What it is | Example |
|-------|------------|---------|
| 🧠 **Knowledge** | A cited design discipline as a guide + do/don't checklist. The "what good looks like." | `open-world-design`, `procedural-generation` |
| ⚙️ **Process** | A workflow verb the agent *runs* — generate, review, plan, analyze. Draws on the knowledge skills. | `procgen-review`, `game-design-process` |

Knowledge skills are the reference. Process skills are the gstack-style commands that *do the work* and cite the reference as they go.

---

## The pipeline

```
  CONCEPT ──▶ WORLD & SYSTEMS ──▶ CONTENT ──▶ REVIEW & GATE ──▶ PLAYTEST & ITERATE
  pillars,    structure, nav,      procgen +   oatmeal / fanfic   benchmarks that
  core loop,  progression,         handcrafted  / sameness /       change the plan
  signature   economy, combat,     anchors,     anti-pattern
  mechanics   systemic rules       lore         gates
```

`game-design-process` is the orchestrator that walks this pipeline and pulls the right skills at each phase. Start there, or invoke any skill directly.

---

## Skills

### Process
| Skill | What it does | Status |
|-------|--------------|--------|
| [`game-design-process`](plugins/gamedev-skills/skills/game-design-process) | The orchestrator. Walks concept → world → content → review → iterate and names which skill to use at each phase. The entry point for "I'm designing a game." | 🟢 v0.1 |
| [`procgen-review`](plugins/gamedev-skills/skills/procgen-review) | The self-review pass for generated content. Runs the oatmeal test, the fanfic/retell test, a cross-instance sameness scan, and the intentionality + anti-pattern gates. Built to run in a headless generation loop. | 🟢 v0.1 |

### Knowledge
| Skill | What it covers | Status |
|-------|----------------|--------|
| [`game-design-fundamentals`](plugins/gamedev-skills/skills/game-design-fundamentals) | The spine the rest of the pack builds on: interesting decisions (Meier), flow & difficulty curves, motivation (Self-Determination Theory), feedback & agency, and choice architecture — with machine-checkable tests for a generation loop. | 🟢 v0.1 |
| [`open-world-design`](plugins/gamedev-skills/skills/open-world-design) | World structure (the triangle rule, gravity, interconnection, biomes, verticality), diegetic navigation, signal color, exploration pull, and spatial pacing. | 🟢 v0.2 |
| [`procedural-generation`](plugins/gamedev-skills/skills/procedural-generation) | Beating the "10,000 bowls of oatmeal" problem: perceptual uniqueness, the handcrafted-anchor + constrained-fill hybrid, voice-consistent corpora, multiplicative systems, intentionality. | 🟢 v0.1 |

More are scaffolded on the [roadmap](#roadmap) and land as research is folded in.

---

## Install

### Plugin marketplace (recommended)

In Claude Code, run:

```
/plugin marketplace add rondorkerin/gamedev-skills
/plugin install gamedev-skills
```

Then confirm it loaded:

```
/plugin
```

You should see **gamedev-skills** enabled, and asking Claude a design question (e.g. *"walk me through designing a procedural open-world RPG"*) should pull in `game-design-process`. Pull new skills as they ship with:

```
/plugin marketplace update gamedev-skills
```

### Manual (single skill, no plugin)

Skills are plain folders — clone and copy the ones you want:

```bash
git clone https://github.com/rondorkerin/gamedev-skills.git
cp -r gamedev-skills/plugins/gamedev-skills/skills/open-world-design ~/.claude/skills/   # all projects
# or into <your-project>/.claude/skills/                                                  # one project only
```

Claude auto-discovers skills in those directories on the next session — verify with `/skills`.

### Headless / Agent SDK

The skills work under `claude -p` and the Agent SDK. With the plugin installed (or the skills copied into `~/.claude/skills/`), point a headless run at the pipeline:

```bash
claude -p "Use the game-design-process skill to design a procedural open-world RPG in Godot. \
Run procgen-review on all generated content before committing it."
```

`game-design-process` sequences concept → world → content → review → iterate, and `procgen-review` gates generated content automatically — no human in the loop.

---

## Using it

Just describe what you're doing:

> "I'm starting a procedural open-world RPG in Godot. Walk me through the design."  → `game-design-process`

> "Lay out the macro-map for the first region."  → `open-world-design`

> "I generated 50 dungeons — check them for sameness before I commit them."  → `procgen-review`

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

---

## Roadmap

Knowledge (from incoming research):
- ✅ Game-design fundamentals · ✅ Open-world design · ✅ Procedural generation
- ⬜ Combat & game feel (juice, telegraphing, encounter design)
- ⬜ RPG systems (progression, economy, loot/itemization)
- ⬜ Systemic / emergent design (immersive sim, multiplicative systems)
- ⬜ Permadeath & lethality (single-save, meta-progression)
- ⬜ Worldbuilding & lore (iceberg, environmental storytelling, mythmaking)
- ⬜ Narrative & quest design (reactivity, the "no fetch quest" rule, factions)
- ⬜ Art direction & readability · ⬜ Pacing & game feel
- ⬜ AI-authored content coherence (corpus + constraints + lore bible)

Process:
- ✅ Game-design process (orchestrator) · ✅ Procgen review
- ⬜ Concept & pillars planning · ⬜ World-layout pass · ⬜ Quest quality gate
- ⬜ Lore-coherence audit · ⬜ Design review (against the anti-pattern catalog) · ⬜ Playtest analysis

---

## Contributing

Research, citations, and production lessons all welcome — see **[CONTRIBUTING.md](CONTRIBUTING.md)** for the skill template and conventions. The pack's knowledge skills are grown from deep-research docs; reusable prompts for generating them live in **[docs/research-prompts.md](docs/research-prompts.md)**.

## License

[MIT](LICENSE) © Arcos Labs. Use it, fork it, ship games with it.
