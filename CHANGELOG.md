# Changelog

All notable changes to this skill pack are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this
project adheres to [Semantic Versioning](https://semver.org/).

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
