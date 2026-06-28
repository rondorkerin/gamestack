# Contributing to gamedev-skills

Thanks for helping build the most useful game-design knowledge pack for Claude. Whether you're adding a brand-new skill, deepening an existing one with research, or fixing a wrong claim — it's all welcome.

## Ground rules

- **Be practical.** Favor advice an actual designer can act on over abstract theory. Every principle should answer "so what do I *do*?"
- **Cite where it matters.** When a claim comes from a talk, postmortem, or book (GDC talks, dev postmortems, design texts), link it. "Trust me" doesn't help Claude reason.
- **Show both sides.** Game design is full of trade-offs. A guide that only lists "best practices" without their failure modes is incomplete. The checklist's do/don't split exists for this reason.
- **No fluff.** These get loaded into a context window. Tight, dense, scannable beats long and padded.

## Anatomy of a skill

Each skill lives at `plugins/gamedev-skills/skills/<skill-name>/` and contains:

| File | Purpose | Loaded |
|------|---------|--------|
| `SKILL.md` | Entry point. YAML frontmatter (`name`, `description`) + a short overview of when to use the skill and how the files relate. | Always (cheap — keep it short) |
| `GUIDE.md` | The reasoning: principles, theory, examples, trade-offs. | On demand |
| `CHECKLIST.md` | Actionable items split into **Do** / **Don't**, grouped by topic. | On demand |

You can add more reference files (e.g. `EXAMPLES.md`, `case-studies/`) and link them from `SKILL.md`.

### SKILL.md frontmatter

```markdown
---
name: open-world-design
description: Use when designing, reviewing, or critiquing an open-world game — world layout, points-of-interest density, navigation/wayfinding, the explore-reward loop, traversal, and systemic/emergent design. Triggers on "open world", "world map", "points of interest", "icon vomit", "make the world feel alive".
---
```

- `name` must match the folder name, be kebab-case, and be unique in the pack.
- `description` is how Claude decides to use the skill. Write it as **"Use when…"** and include concrete trigger phrases. This is the single most important line for discoverability.

## Adding a new skill

1. Copy the structure of `open-world-design` as a template.
2. Write `SKILL.md`, `GUIDE.md`, `CHECKLIST.md`.
3. Add a row to the **Skills** table in `README.md` and tick it on the roadmap.
4. Add an entry to `CHANGELOG.md`.
5. Open a PR. Describe what the skill covers and where the knowledge comes from.

## Deepening an existing skill

Got research, a postmortem breakdown, or production experience? Open a PR that expands the relevant `GUIDE.md` / `CHECKLIST.md` and bump the skill's status note. Keep the structure intact so it stays scannable.

## Style

- Markdown, GitHub-flavored.
- Prefer tables and tight bullets over walls of prose.
- Concrete game examples (named titles, named techniques) over generic statements.
- Present tense, second person ("you"), imperative in checklists.

## Local testing

Install the pack locally and confirm Claude picks up your skill:

```
/plugin marketplace add /absolute/path/to/this/repo
/plugin install gamedev-skills
```

Then ask Claude a question that should trigger your skill and confirm it loads.
