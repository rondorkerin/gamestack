---
name: game-design-process
description: The orchestrator for designing a game end to end. Use when starting or steering a game's design — "I'm making a game", "where do I start", "design this game with me", "what's next in the design", or running headless to take a concept to generated, reviewed content. Engine-agnostic; tuned for procedural / AI-authored games (e.g. a procgen open-world RPG in Godot). Sequences the pipeline and names which knowledge/process skill to pull at each phase. Stops at design/spec — does not write gameplay code.
---

# Game-Design Process

The spine of the pack. This skill turns a one-line game concept into a designed, generated, self-reviewed game — and tells you which other skill does the work at each step.

## When to use this

- Kicking off a new game's design, or unsure what the next design move is
- Running headless: an agent loop that designs and generates content without a human in the seat
- Doing a design-health pass on an existing game ("which phase did we skip?")

## The core stance

1. **Design from interlocking systems and constraints, not content volume.** A headless/procedural agent's superpower is authoring *rules* that generate content, not hand-placing it. Lean into systemic and procedural design.
2. **Every element must earn its place.** Each system answers "what interesting decision does this create?"; each generated location answers "who made this and what happened here?" If neither, cut it.
3. **Hand-author a static backbone; proceduralize the connective tissue.** Finite legendary loot, named landmarks, the mythic spine, key quests = hand-authored anchors. Everything between = constrained generation.
4. **Gate generated content against hard quality bars** before it's committed. This is non-negotiable for procgen — see `procgen-review`.
5. **Stop at the spec, then hand off.** This pipeline produces world bibles, system specs, content, and review verdicts. Implementation is a separate, engine-specific step — when the spec is ready, pass it to `engine-router`, which routes the build to the matching engine pack (Godot/Unreal/Unity).

## The pipeline

Read **`PIPELINE.md`** for the full phase-by-phase process with entry/exit gates. In brief:

| Phase | Goal | Skills to pull |
|-------|------|----------------|
| **1 · Concept** | Fantasy, 3–5 pillars, core loop, signature mechanics. Prove each is an "interesting decision." | `game-design-fundamentals` |
| **2 · World & systems** | World structure & navigation; progression, economy, combat; the systemic ruleset. | `open-world-design`, `rpg-systems`, `combat-design`, `systemic-emergent-design` |
| **3 · Content** | Lore bible + constraint set; generate world/quests/lore from handcrafted anchors + constrained fill. | `procedural-generation`, `worldbuilding-and-lore`, `narrative-and-quest-design`, `ai-authored-content` |
| **4 · Review & gate** | Run the quality bars: oatmeal, fanfic/retell, cross-instance sameness, intentionality, anti-patterns. | `procgen-review`, `design-review` |
| **5 · Playtest & iterate** | Watch the benchmarks; feed failures back to the phase that owns them. | `procgen-review` (loop), playtest analysis |

> Skills marked above that don't exist yet are on the roadmap — use the matching section of `open-world-design` / `procedural-generation` / `procgen-review` in the meantime, and the principles in `PIPELINE.md`.

## The one rule for headless loops

> **Never commit generated content that hasn't passed `procgen-review`.** Generation is cheap and confident; sameness is invisible from inside a single sample. The review pass is what stands in for the human designer's eye.

Start at `PIPELINE.md` Phase 1, or jump to the phase you're in.
