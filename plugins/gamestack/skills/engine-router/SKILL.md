---
name: engine-router
description: The platform router for the gamestack framework. Use when a game's design is ready to implement, when the target engine is chosen or needs choosing, or when handing off a spec to engine code — "build this in Godot/Unity/Unreal/Three.js", "which engine", "now implement it", "wire the design to code". Routes design phases to gamestack's foundation skills and implementation to the matching engine pack (godot, unreal, unity). Engine-agnostic itself; it decides where work goes.
---

# Engine Router

gamestack splits a game into two jobs: the **design brain** (engine-agnostic, first-party) and the **engine hands** (per-platform, the curated engine packs). This skill is the dispatcher between them — it keeps design decisions in one place and sends implementation to whichever engine pack owns the target platform.

## When to use this

- A design/spec is ready and it's time to write engine code
- Choosing a target engine, or supporting more than one
- Mid-build, deciding "is this a design question or an engine question?" and pulling the right skill

## The core stance

1. **Design once, implement per engine.** The world bible, systems, combat feel, and procgen rules from the foundation skills are engine-independent. Don't re-derive them inside an engine — translate them.
2. **Never put design logic in an engine skill, or engine APIs in a design skill.** If you're reaching for `Node`/`Actor`/`THREE.Scene` while still deciding *what interesting decision a system creates*, stop — that's a foundation question (`game-design-fundamentals`).
3. **One spec, one handoff artifact.** The design pipeline's output (specs, content, quality verdicts) is the contract the engine pack consumes. Engine choice never changes the spec.

## Routing table

| Phase / question | Layer | Skill to pull |
|------------------|-------|---------------|
| Concept, pillars, core loop, "is this an interesting decision?" | Foundation (design) | `game-design-fundamentals` |
| World structure, navigation, spatial pacing | Foundation (design) | `open-world-design` |
| Generating + reviewing content without sameness | Foundation (design) | `procedural-generation`, `procgen-review` |
| Combat & game feel (juice, telegraphing, encounters) | Foundation (design) | `combat-design` |
| Sequencing the whole design end to end | Foundation (process) | `game-design-process` |
| **Implement in Godot 4.x** (GDScript, systems, optimization, export) | Engine hands | `godot` pack (`/plugin install godot@gamestack`) |
| **Implement in Unreal** (C++ gameplay framework, rendering, networking) | Engine hands | `unreal` pack (`/plugin install unreal@gamestack`) |
| **Debug a Unity build** (logging, runtime commands, watching) | Engine hands | `unity-jahro` pack (`/plugin install unity-jahro@gamestack`) |
| **Implement in Unity (general authoring)** | Engine hands | ⬜ roadmap — no curated pack yet; use general C#/Unity knowledge + the foundation specs |
| **Implement in Three.js / web** | Engine hands | ⬜ roadmap — no curated pack yet; use the foundation specs + general Three.js knowledge |

## The handoff procedure

1. **Confirm the engine.** If unset, ask (or read it from the project: a `project.godot`, `*.uproject`, `Assets/` + `ProjectSettings/`, or `package.json` with `three`). Don't guess silently.
2. **Confirm the spec exists.** Implementation consumes the design pipeline's output. If there's no spec yet, route *back* to `game-design-process` first — don't improvise design inside engine code.
3. **Install / confirm the engine pack** for the target (table above). If the platform is a roadmap gap, say so explicitly and fall back to the foundation specs plus general engine knowledge — never silently pretend a pack exists.
4. **Translate, don't redesign.** Map each spec element to the engine pack's matching skill (e.g. an ability system spec → the engine pack's ability/component skills). Design intent is fixed; only the implementation is engine-specific.
5. **Keep the loop closed.** Bugs in *feel* or *balance* go back to the foundation skill that owns them; bugs in *implementation* stay in the engine pack.

## The one rule

> **The spec is engine-independent; the code is engine-specific. This skill is the only place the two meet.** Cross-contaminate them and you'll be redoing design work in every engine you port to.
