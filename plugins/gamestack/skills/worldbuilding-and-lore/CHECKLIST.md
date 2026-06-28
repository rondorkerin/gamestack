# Worldbuilding & Lore Delivery — Checklist

Actionable **Do / Don't** + **Test-for** for a world bible and its lore delivery. Run against the canon graph, a region, or a single artifact. See `GUIDE.md` for reasoning and sources. **(extrapolation — test it)** marks AI-authoring moves that are engineering hypotheses, not validated practice.

---

## Iceberg — build the whole, ship the tip

**Do**
- [ ] Author the full backbone (cosmology, timeline, factions, geography, economy, magic laws) as **frozen, typed canon** before any player-facing artifact.
- [ ] Reveal on a strict budget (target ~10–12% visible); pick the **smallest artifact** that conveys the needed inference.
- [ ] Tag every unrevealed fact `INTENTIONALLY_OMITTED` or `HIDDEN_BUT_KNOWABLE`.
- [ ] Surround every perceptible gap with **≥2 anchored facts**.

**Don't**
- [ ] Don't omit what you never authored (that's a hollow place, not mystery).
- [ ] Don't front-load the submerged mass (info-dump / worldbuilder's disease).
- [ ] Don't ship a fact tagged `UNDECIDED`.
- [ ] **(extrapolation — test it)** Don't let the generator invent a backbone fact while writing a surface artifact — generate the berg as data, freeze, then read-only.

**Test for** — Does every artifact resolve to ≥1 canonical entity ID, with zero references to non-existent entities? Is every artifact under the word cap? Is each fact delivered through only one explicit exposition channel?

---

## Show, don't tell — environmental & item delivery

**Do**
- [ ] Default every lore beat to **environment or item-description**; justify any cutscene/expository dialogue.
- [ ] Tag every space/artifact by Jenkins mode (**evocative / embedded / enacted / emergent**).
- [ ] Give each region ≥1 evocative cue, ≥1 embedded clue, ≥1 enacted micronarrative.
- [ ] Write descriptions in an **in-world voice**; include ≥1 unexplained proper noun resolving to canon.

**Don't**
- [ ] Don't fall back on mechanical exposition through cut scenes.
- [ ] Don't ship evocative dressing with 0 embedded clues (wallpaper world).
- [ ] Don't write flat omniscient "wiki voice" that forecloses interpretation.

**Test for** — Are ≥70% of completionist-reachable facts diegetic, and is the critical path comprehensible with zero optional lore? Any region with 0 embedded clues? Does each description name/imply a source perspective?

---

## Identity — "alien but coherent"

**Do**
- [ ] Encode the register as a **finite style grammar** (materials, silhouettes, naming morphology, taboo tropes).
- [ ] Express world logic as **machine-checkable invariants** and validate every artifact.
- [ ] Anchor every alien concept to **exactly one** familiar emotional/functional handhold.
- [ ] Hand-author the seed grammar (30–50 rules) and art vocabulary; let the model only **sample within** it.

**Don't**
- [ ] Don't let the training prior pull you to Euro-medieval defaults (generic-fantasy regression).
- [ ] Don't accumulate plausible-but-contradictory additions (canon drift).
- [ ] Don't stack unfamiliar layers with no handhold (opaque alienness).

**Test for** — Does every name/asset/place pass the style-grammar validator? Would any artifact also fit an off-the-shelf generic fantasy game (flag it)? Does the contradiction-checker return zero invariant violations corpus-wide? Is every alien noun paired with a concrete sensory/human detail?

---

## Deep history & mythmaking

**Do**
- [ ] Make each deity/myth a **faction proxy**: ≥1 faction, ≥1 region, ≥1 mechanic.
- [ ] Author history as a **causal chain** (event → present-day condition), not a list of names.
- [ ] Grow the iceberg **macro→micro** (cosmology → geography → peoples → culture → locale → artifact); each level cites its parent.
- [ ] Propagate one **"Element X"** through every level.
- [ ] Hand-author the top ~3 levels (cosmology, geography skeleton, founding myths, Element X); generate consequences below.

**Don't**
- [ ] Don't ship decorative gods that never touch play.
- [ ] Don't ship a lore calendar (dated names, no observable traces).
- [ ] Don't generate cool locales first and back-fill cosmology (bottom-up incoherence).

**Test for** — Does every deity resolve to a faction + region + mechanic? Does every historical event leave ≥1 present-day trace? Does the pipeline run in macro→micro order with parent citations?

---

## Ludonarrative harmony — mechanics that are the story

**Do**
- [ ] Write a one-sentence **ludic contract** and **narrative contract** for each core mechanic; flag `{ALIGNED | INTENTIONAL_TENSION}`.
- [ ] Link every system (loot, death, economy, status) to ≥1 canon entity that explains *why it works that way*.
- [ ] Make each constraint (finite loot, permadeath, token economy) map to a **theme + a canon institution/event**.
- [ ] Prefer diegetic delivery; log every non-diegetic UI element with a justification or accessibility-exception tag.
- [ ] Maintain a machine-checked **mechanic↔lore mapping table**; keep a global **finite-loot ledger** as canon.

**Don't**
- [ ] Don't ship an *unintended* ludonarrative mismatch.
- [ ] Don't ship "gamey" systems (respawn, fast travel) with no diegetic account.
- [ ] Don't justify constraints with developer hostility — give each a narrative reason.
- [ ] Don't let quest markers let players complete content while skipping all lore.
- [ ] Don't ever mint a duplicate legendary — halt if the ledger breaks.

**Test for** — Does every core mechanic have a contract pair and no `UNRESOLVED` flag at ship? Does every mechanic link to canon? Would removing a constraint falsify a stated theme? Is "mechanic with no canon link" treated as a build-breaker?

---

## The gating discipline (for headless loops)

- [ ] Build validators (contradiction, style-grammar, prior-leakage, coverage) **before** generators.
- [ ] Run every artifact through all validators; nothing ships un-gated (→ `procgen-review`).
- [ ] Watch the thresholds: contradiction rate >~2% → stop scaling; gaps lacking anchors >~30% → ambiguity unbounded; regression climbing → prior is winning; duplicate legendary → halt.

**Test for (the key one)** — Before scaling generation: do the validators exist and pass on the current corpus? If not, you are generating onto an unguarded canon — the exact condition under which an LLM produces Hemingway's "hollow places" at scale.
