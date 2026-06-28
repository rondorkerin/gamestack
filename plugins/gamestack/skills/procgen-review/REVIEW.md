# Procgen Review — Procedure

The step-by-step review pass for a batch of generated content. Run it before committing, ideally automatically inside a headless generate→review→repair loop.

> **Why this exists:** a generator producing one artifact at a time has no way to see that its 11th dungeon is the same as its 3rd, or that its output is technically unique but perceptually oatmeal. This pass is the designer's eye the loop is missing. (Kate Compton's perceptual-uniqueness metric; the explicit self-review pass the open-world bible recommends.)

---

## Inputs

- `batch` — the generated artifacts. **Need ≥8 instances of one type** for the sameness scan to mean anything; with fewer, run gates 1–2 and 4–5 only and flag low confidence.
- `constraints` — the generator's definition of "good" and its "never violate" list.
- `priors` — previously committed instances of the same type (for cross-instance comparison).
- `region_context` — the lore/biome/canon each artifact is supposed to fit.

## Verdict scale

- **PASS** — commit it.
- **SOFT-FAIL** — commit allowed but flagged; log the weakness and the suggested improvement.
- **HARD-FAIL** — blocks commit. Return to the generator with the specific fix.

Run all five gates on every artifact. A HARD-FAIL on any gate fails the artifact.

---

## Gate 1 · Oatmeal test (perceptual uniqueness)

**Question:** are these perceptually distinct, or mathematically-unique sameness?

**Procedure:**
1. Sample N artifacts from the batch (N ≥ 8 if available). Strip identifiers/seeds.
2. For each pair, ask: stripped of names and numbers, could a player tell these two apart by *experience* — layout, mood, the beat that makes it memorable? Not "are the values different" (they always are) but "does it *feel* different."
3. Score the batch's distinctness ratio: distinguishable pairs / total pairs.

**Verdict:**
- < 0.5 distinguishable → **HARD-FAIL** (batch is oatmeal).
- 0.5–0.8 → **SOFT-FAIL** (thin variety; note which dimensions collapse — usually layout or reward).
- > 0.8 → PASS.

**Fix routing:** add per-biome generation rules, deepen the corpus, or raise the handcrafted-anchor ratio (`procedural-generation` §3–4).

---

## Gate 2 · Fanfic / retell test (is it worth it?)

**Question:** would a player care enough to *retell* this to someone?

**Procedure:** for each artifact, attempt a one-sentence retelling a player would actually say ("the crypt where the dead king's crown was guarded by his own betrayed knights"). If the only honest retelling is generic ("a dungeon with some skeletons and a chest"), it fails.

**Verdict:**
- No non-generic retelling possible → **SOFT-FAIL** (raise systemic depth or anchor it; a single bland instance isn't fatal).
- A whole batch with no retellable instances → **HARD-FAIL**.

**Fix routing:** increase systemic depth / multiplicative interactions (`systemic-emergent-design`), or attach a hand-authored hook (`procedural-generation` §6).

---

## Gate 3 · Cross-instance sameness scan (the one a generator can't self-do)

**Question:** does this duplicate something already committed?

**Procedure:** compare each new artifact against `priors` on three axes:
1. **Structural** — same layout skeleton / room graph / encounter shape.
2. **Thematic** — same lore beat, same "who/what/why," same twist.
3. **Reward** — same payoff in the same role.

Flag any new artifact matching a prior on **≥2 axes**.

**Verdict:**
- Matches a prior on all 3 axes → **HARD-FAIL** (it's a copy).
- Matches on 2 axes → **SOFT-FAIL** (differentiate at least one axis before commit).
- ≤1 axis → PASS.

**Note:** also scan *within* the batch, not just against priors. A batch that's internally diverse but converges over time still produces the "skip the 11th" problem.

**Fix routing:** widen the generation space on the colliding axis; add deck-shuffle / draw-without-replacement so recently-used templates are suppressed (`procedural-generation` §8).

---

## Gate 4 · Intentionality gate (local logic & arc)

**Question:** is this an authored *place*, or just geometry?

**Checklist per artifact (all must hold):**
- [ ] **Who built it / who's here** — answerable from the content itself.
- [ ] **What happened here** — an implied history (environmental storytelling, not a lore dump).
- [ ] **Why this reward here** — reward thematically tied to the location type and region-appropriate.
- [ ] **Connects to neighbors** — consistent with adjacent regions/canon.
- [ ] **Completion arc** — a clear beginning/payoff/end (e.g. ends in a boss + safe node).

**Verdict:**
- Missing who/what/why **or** reward-fit → **HARD-FAIL**.
- Missing completion arc or weak neighbor-fit → **SOFT-FAIL**.
- All hold → PASS.

**Fix routing:** generate *reasons*, not just geometry (`procedural-generation` §6); check canon against the constraint set (`ai-authored-content`).

---

## Gate 5 · Anti-pattern gate

**Question:** has the batch fallen into a named failure mode?

Scan against the catalog (full descriptions in `open-world-design` and `procedural-generation`):

| Anti-pattern | Trip condition | Severity |
|---|---|---|
| Copy-paste content | Gate 3 all-axis match | HARD |
| Oatmeal | Gate 1 < 0.5 | HARD |
| Geometry without reason | Gate 4 who/what/why missing | HARD |
| Canon contradiction | Violates a "never" constraint | HARD |
| Reward disconnected from context | Gate 4 reward-fit missing | HARD |
| Wide-but-shallow | High volume, low Gate 1/2 scores | SOFT |
| Icon-janitor filler | Content with no in-world meaning to its existence | SOFT |
| Decorative-only procgen | Output doesn't correlate with mechanics | SOFT |
| Samey biomes | Region identity not distinct (spatial batches) | SOFT |
| Dead travel | Spatial batch: empty stretches between POIs | SOFT |

Any HARD trip → artifact HARD-FAILs.

---

## Output format

Emit a structured verdict the loop can act on:

```json
{
  "batch_id": "...",
  "type": "dungeon|region|quest|item|npc|lore",
  "summary": { "pass": 0, "soft_fail": 0, "hard_fail": 0, "oatmeal_distinctness": 0.0 },
  "artifacts": [
    {
      "id": "...",
      "verdict": "PASS|SOFT-FAIL|HARD-FAIL",
      "gates": {
        "oatmeal": "PASS",
        "fanfic": "PASS",
        "sameness": "SOFT-FAIL: matches prior #14 on structure+reward",
        "intentionality": "PASS",
        "antipattern": "PASS"
      },
      "fixes": [
        { "issue": "...", "fix": "...", "route_to": "procedural-generation §8" }
      ]
    }
  ],
  "batch_recommendation": "COMMIT | COMMIT_WITH_FLAGS | REGENERATE | SHIFT_ANCHOR_RATIO"
}
```

`batch_recommendation` escalates: if HARD-FAILs exceed ~30% of the batch *after* a regeneration attempt, recommend **SHIFT_ANCHOR_RATIO** — the generator can't reach quality through tuning alone and needs more hand-authored anchors (`game-design-process` thresholds).

---

## Headless loop integration

```
generate(batch) → procgen-review(batch) →
   PASS            → commit
   SOFT-FAIL       → commit + log improvement backlog
   HARD-FAIL       → regenerate with fix; re-review (cap retries, e.g. 3)
   3× HARD-FAIL    → escalate: SHIFT_ANCHOR_RATIO or surface to a human
```

Cap retries so a stuck generator escalates instead of looping forever. Log every SOFT-FAIL — the backlog is your signal for where the generator needs the most work.
