# gamestack preamble (run this before the skill body)

This file is loaded at the top of every gamestack process skill. It is a procedure to
**execute**, not text to display. Do these four steps, briefly, then continue into the
skill.

## 1. Load the design bible (persistent memory)

The bible lives in the user's game project at `./.gamestack/bible/` (cwd is the game
project, NOT the plugin). It is gamestack's cross-session memory and the engine-handoff
contract.

- If `./.gamestack/bible/` is missing, create it with empty stub files:
  `pillars.md world.md systems.md lore.md constraints.md decisions.md` and a `corpus/`
  dir and an `engine` file. Say "Initialized a new design bible."
- If it exists, read the files relevant to this skill and give a one-line state summary
  (e.g. "Bible: 3 pillars set, world + systems drafted, 12 decisions logged").
- When this skill makes a design decision, append it to `./.gamestack/bible/decisions.md`
  using the Edit/Write tool (never a raw shell `>>` — escaping hazard). Format:
  a dated `### <date> — <decision>` heading, a one-line rationale, and a
  `supersedes:` line if it overrides an earlier decision.

## 2. Detect the target engine and load its overlay

Precedence:
1. If `./.gamestack/bible/engine` names an engine, use it.
2. Else auto-detect from the project: `project.godot` → godot; `*.uproject` → unreal;
   `Assets/` + `ProjectSettings/` → unity; `package.json` containing `"three"` → threejs.
3. Else ask the user once, and write the answer to `./.gamestack/bible/engine`.

Then read the matching overlay: `${CLAUDE_PLUGIN_ROOT}/overlays/<engine>.md`. It maps
design specs to that engine's pack skills. If the engine has no overlay yet
(roadmap), say so and proceed with the engine-agnostic design only.

## 3. Hold the completeness principle

> No 10,000 bowls of oatmeal. Completeness in games is perceptual uniqueness +
> intentionality, NOT content volume. Cut content that does not create an interesting
> decision or answer "who made this and what happened here?"

The full ethos is in `${CLAUDE_PLUGIN_ROOT}/ETHOS.md` (loaded alongside this file).

## 4. Completion-status protocol

End the skill by reporting one status: **DONE** (with evidence) / **DONE_WITH_CONCERNS**
(list them) / **BLOCKED** (state the blocker + what you tried) / **NEEDS_CONTEXT** (state
exactly what is missing). Each gamestack process skill defines its own structured-output
section that conforms to this protocol — that section is the per-skill output, not a
second declaration of the protocol.
