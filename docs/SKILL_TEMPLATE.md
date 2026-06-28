# Skill authoring template

Copy this structure to start a new skill. See `CONTRIBUTING.md` for conventions.

```
plugins/gamestack/skills/<your-skill-name>/
├── SKILL.md
├── GUIDE.md
└── CHECKLIST.md
```

---

## `SKILL.md`

```markdown
---
name: your-skill-name
description: Use when <situations>. Triggers on "<phrase>", "<phrase>", "<phrase>".
---

# <Title>

One-line statement of what this skill gives Claude.

## When to use this
- <situation>
- <situation>

## How the pieces fit
- **`GUIDE.md`** — the *why*: principles and trade-offs.
- **`CHECKLIST.md`** — the *what to do*: actionable Do/Don't items.

## The one idea to anchor on
> <The single most important principle, stated memorably.>
```

---

## `GUIDE.md`

Sectioned explanation of the *why*. Each section: the principle, an example
(named game/technique), and the trade-off or failure mode. End with a
"common failure modes" table and a practical design sequence if it helps.

---

## `CHECKLIST.md`

Grouped **Do / Don't** items mirroring the guide's sections. Keep each item
to one actionable line. This is what a designer scans during review.
