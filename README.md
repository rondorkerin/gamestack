# 🎮 gamedev-skills

**A comprehensive, open-source skill pack for game development with [Claude](https://claude.com/claude-code).**

Drop these skills into Claude Code (or Claude Desktop) and your agent gains structured, battle-tested knowledge of game design — design techniques, systems thinking, level design, and production craft. Every skill pairs a **practical guide** ("here's how this works and why") with an **actionable do/don't checklist** ("here's what to actually do, and the traps to avoid").

This pack is built to grow. It starts with open-world design and expands into a full game-design knowledge base, contributed by the community.

---

## Skills

| Skill | What it covers | Status |
|-------|----------------|--------|
| [`open-world-design`](plugins/gamedev-skills/skills/open-world-design) | Designing open worlds that feel alive instead of empty: navigation, points-of-interest density, the compulsion loop, systemic/emergent design, traversal, and the "icon vomit" trap. Guide + checklist. | 🟡 v0.1 (expanding) |

More skills are in progress — see the [roadmap](#roadmap).

---

## Install

### Option A — Plugin marketplace (recommended, one-time setup)

In Claude Code:

```
/plugin marketplace add arcoslabs/gamedev-skills
/plugin install gamedev-skills
```

That's it. Every skill in the pack is now available to Claude, and `/plugin marketplace update gamedev-skills` pulls new skills as they ship.

> Replace `arcoslabs/gamedev-skills` with the actual `owner/repo` once this is pushed to GitHub.

### Option B — Manual (copy a single skill)

Skills are just folders. Copy the one you want into your skills directory:

```bash
# Project-scoped (only this repo's Claude sees it)
cp -r plugins/gamedev-skills/skills/open-world-design /path/to/your/project/.claude/skills/

# User-scoped (all your projects)
cp -r plugins/gamedev-skills/skills/open-world-design ~/.claude/skills/
```

Claude auto-discovers skills in those directories on the next session.

---

## How to use a skill

Once installed, just ask Claude naturally:

> "Help me design the points-of-interest layout for the first region of my open world."

> "Review my world map — is it falling into the Ubisoft-tower / icon-vomit trap?"

Claude will pull in the relevant skill automatically. You can also invoke it directly with `/open-world-design`.

---

## What's in a skill

Each skill is a folder with a consistent shape:

```
skills/<skill-name>/
├── SKILL.md       # Entry point: when to use it, how the pieces fit together
├── GUIDE.md       # The "why" — principles, theory, worked reasoning
└── CHECKLIST.md   # The "what to do" — actionable do/don't items
```

`SKILL.md` carries the YAML frontmatter (`name`, `description`) Claude uses to decide when the skill applies. The guide and checklist are loaded on demand so the skill stays cheap until it's needed.

---

## Roadmap

Planned skills (contributions welcome — see [CONTRIBUTING.md](CONTRIBUTING.md)):

- ✅ Open-world design
- ⬜ Level design & spatial storytelling
- ⬜ Combat & game feel ("juice")
- ⬜ Progression & reward systems / economy design
- ⬜ Narrative & quest design
- ⬜ Systemic / emergent design
- ⬜ Difficulty, onboarding & player psychology
- ⬜ Multiplayer & live-ops design
- ⬜ Procedural generation
- ⬜ Playtesting & metrics

---

## Contributing

This is a community knowledge pack — research, citations, and hard-won production lessons all welcome. See **[CONTRIBUTING.md](CONTRIBUTING.md)** for the skill template and authoring conventions.

## License

[MIT](LICENSE) © Arcos Labs. Use it, fork it, ship games with it.
