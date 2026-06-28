# Game-Design Pipeline

The end-to-end process for designing a game, with the skill to pull and the gate that must pass before moving on. Optimized for headless, procedural, AI-authored development.

> Each phase has an **entry gate** (don't start until this is true) and an **exit gate** (don't advance until this is true). In a headless loop, treat the exit gates as hard asserts.

---

## Phase 1 · Concept

**Goal:** a tight statement of what the game *is* before any world or systems exist.

Produce:
- **Player fantasy** — one sentence: who the player is and what the game makes them feel.
- **3–5 pillars** — the load-bearing experiences. Every later decision serves these.
- **Core loop** — the moment-to-moment cycle (act → reward → re-prime).
- **Signature mechanics** — the 1–3 things this game does that others don't. (For the running example: finite legendary loot, single-save high-lethality, a diegetic token economy, an AI-authored world.)

Test (the spine of the whole pack — Sid Meier): for every pillar and mechanic, **"what interesting decision does this create?"** A decision is interesting only if it has believed consequences, is predictable enough to reason about, carries a real trade-off, and has no dominant option. If a mechanic creates no interesting decision, cut or merge it.

- **Skill:** `game-design-fundamentals` (flow, difficulty curves, reward schedules, interesting decisions).
- **Exit gate:** pillars written; core loop diagrammed; each signature mechanic passes the interesting-decision test.

---

## Phase 2 · World & systems

**Goal:** the rules and the space, before any content fills them.

Two parallel tracks:

**A. World structure & navigation** → `open-world-design`
- Macro-layout via the triangle rule (large landmarks / medium occluders / small texture).
- One mega-landmark visible from most of the map for constant orientation.
- Distinct biomes (palette, flora, architecture, silhouette); verticality as a real axis.
- Diegetic navigation over HUD markers; a reserved signal color that contrasts in every biome.
- Spatial pacing: a wonder→fear gradient and tension/release with safe hubs.

**B. Systems** → `rpg-systems`, `combat-design`, `systemic-emergent-design`
- Progression that makes the player *feel* stronger; **spatial gating, not level-scaling**.
- Economy on a faucet/sink model; with finite supply, scarcity *is* value — prioritize sinks.
- Combat readability first (telegraphing, silhouettes) — in a lethal game this is fairness, not polish.
- A small set of **multiplicative** systems (a chemistry/physics ruleset) that combine to produce solutions you didn't script. This is the antidote to templated content.

Rule: **every system should interact with at least two others.** Author affordances, not solutions.

- **Exit gate:** world skeleton laid out; systems specified; the multiplicative ruleset defined; a sample obstacle has ≥2 valid solutions.

---

## Phase 3 · Content

**Goal:** fill the world — mostly procedurally, always anchored.

1. **Lore bible + constraint set** → `worldbuilding-and-lore`, `ai-authored-content-coherence`
   - Build deep, reveal little (the iceberg). Encode canon as a machine-readable constraint set with a "never violate" list.
   - Define the world's *voice* and seed a generation corpus so all generated text shares one identity.
2. **Hand-author the static backbone** → finite legendary items (each named, with a lore hook and a build-defining effect), named landmarks, the mythic spine, key quests.
3. **Generate the connective tissue** → `procedural-generation`
   - Constrained fill between anchors. Each location carries local logic ("who built this, what happened here"), region-appropriate rewards, and a clean completion arc.
   - History references only already-established canon (generate, then rationalize).
4. **Quests** → `narrative-and-quest-design`
   - No fetch quests: every quest needs a twist, choice, character, or consequence. Procedural/radiant quests are texture, never the backbone.
   - Track world state in a facts-database so the world reacts to player choices.
5. **Lore delivery** → environmental storytelling and item descriptions; forbid lore-dump dialogue.

- **Exit gate:** backbone hand-authored; generators produce content that respects the constraint set, local logic, and reward-fit. **Do not advance unreviewed content** — go to Phase 4.

---

## Phase 4 · Review & gate

**Goal:** catch what generation can't see about itself. **This phase is mandatory for procedural content.**

Run `procgen-review` on every batch of generated content:
- **Oatmeal test** — sample outputs blind; can they be told apart? Mathematical uniqueness ≠ perceptual uniqueness.
- **Fanfic/retell test** — would a player retell this location/quest/item? If not, raise systemic depth.
- **Cross-instance sameness scan** — compare each new instance against prior ones; flag structural/thematic duplication (the failure a human designer would catch and a single-sample generator can't).
- **Intentionality gate** — does each location answer who/what/why and have a completion arc?
- **Anti-pattern gate** — check against the named failure modes (icon vomit, copy-paste, dead travel, fetch-filler, level-scaling, lore-dumps, samey biomes).

Run `design-review` on the design itself against the same anti-pattern catalog.

- **Exit gate:** every committed artifact passes its rubric, or is sent back to the phase that owns the fix (see thresholds below).

---

## Phase 5 · Playtest & iterate

**Goal:** let observed behavior rewrite the plan.

Watch for these signals and route each to the phase that owns it:

| Signal | Likely cause | Route to |
|--------|-------------|----------|
| Players cluster on a few paths, ignore the rest | Weak landmark gravity | Phase 2A · `open-world-design` |
| Players watch the minimap, not the world | Too much HUD guidance | Phase 2A · diegetic nav |
| Generated content feels samey (players skip the 11th) | Procgen sameness | Phase 3 · more anchors / corpus depth; re-gate in Phase 4 |
| No sense of progression | Inadvertent level-scaling | Phase 2B · spatial gating |
| Exploration feels unrewarding | Reward cadence too sparse | Phase 2A · micro-reward density |
| Players quit in frustration, not "one more try" | Unfair lethality / weak readability | Phase 2B · combat readability, or add diegetic persistence |
| Economy inflates / hoards | Faucet/sink imbalance | Phase 2B · adjust sinks before drop rates |

---

## Thresholds that change the whole plan

- **Procedural content fails the oatmeal/fanfic tests after corpus + constraint tuning** → shift the handcrafted:procedural ratio toward more hand-authored anchors (the Caves of Qud / Spelunky model).
- **Single-save causes unacceptable churn** → add diegetically-framed meta-progression *before* weakening lethality.
- **AI-generated narrative drifts incoherent** → tighten the constraint set and shrink generation scope; coherence at scale is unsolved — de-risk early with the oatmeal/fanfic tests.

---

*This pipeline is engine-agnostic. The output of every phase is a spec or content artifact, not code. Hand those artifacts to your implementation step (Godot, etc.) separately.*
