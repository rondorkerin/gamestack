# Worldbuilding & Lore Delivery — Guide

Author the whole world, ship the tip, and deliver it through objects and space — in a way that survives procedural generation. Pair with `CHECKLIST.md` for the actionable version.

> **The central law:** build the submerged 90% as **frozen, typed, queryable canon** *before* generating any player-facing artifact; then generate only **leaves** that read from that canon, never roots. This is the one move that turns the iceberg principle — the most dangerous principle for an LLM author — into something a generator can't violate.

> **Reading the tags.** This guide is distilled from a complete deep-research synthesis with its own caveats (carried in §6), not the truncated-verification batch — so it does not use per-claim ✅/⚠️/❌ tags. Where a claim is craft-lore stated as an absolute, or an AI-authoring extrapolation, it is marked **(extrapolation — test it)** inline.

---

## 1. The iceberg principle — build the whole, ship the tip

**Rule:** Author a complete, internally consistent world bible (cosmology, timeline, factions, geography, economy, magic/tech laws) as canonical ground truth *before* generating any player-facing artifact; then reveal on a strict budget (target ~10–12% visible), preferring the smallest artifact that conveys the needed inference.

**Exemplar:** Hemingway's "theory of omission" (*Death in the Afternoon*, 1932, ch. 16): a writer who knows enough "may omit things that he knows and the reader… will have a feeling of those things… A writer who omits things because he does not know them only makes hollow places." Miyazaki keeps a "perfect storyline" for Dark Souls he has "no intention of enforcing" — the berg exists though never fully shown. Sanderson's "expand what you have before you add" (Third Law) and conveying magic by *feel* (Mistborn's metallic taste) over encyclopedic dumps.

**Test for:** every player-facing artifact resolves to ≥1 canonical entity ID; **zero** artifacts reference an entity not in canon. No single artifact exceeds a hard word cap (e.g. ≤120 words); no fact is delivered through more than one explicit exposition channel.

**Failure modes:**
- **Hollow iceberg** — omitting what the author never knew, so gaps read as contradiction/emptiness, not mystery.
- **Info-dump / "worldbuilder's disease"** (Sanderson) — front-loading the submerged mass, collapsing mystery and pacing.

**Tag your gaps.** Mark each unrevealed fact `INTENTIONALLY_OMITTED` (player infers) or `HIDDEN_BUT_KNOWABLE` (discoverable via artifacts); never ship a fact `UNDECIDED`. Miyazaki's design recreates reading books "too hard for me… using my imagination to fill in the gaps" (PlayStation Blog, E3 2018) — but gaps work only when **bounded**: every perceptible gap needs ≥2 surrounding anchored facts. Gaps with no anchors = **unbounded ambiguity** (the "Elden Ring obfuscates motivations" critique), which reads as incoherence, not mystery.

> **Procedural / AI implication (extrapolation — test it).** The iceberg is the single most dangerous principle for an LLM, because the model's natural failure *is* Hemingway's "omitting what you do not know." Make the submerged 90% an **explicit typed data structure** (a canon graph), not latent model knowledge. Generate the berg first *as data*, **freeze it**, then generate surface artifacts that only read from frozen canon. Never let the model invent a backbone fact while writing an item description — that is how contradictions enter.

---

## 2. Show, don't tell — environmental & item-description storytelling

**Rule:** Default every lore beat to an environmental or item-description channel; require explicit justification before using a cutscene or expository dialogue. Henry Jenkins ("Game Design as Narrative Architecture," 2004) reframes designers as **narrative architects**, with four delivery modes — tag every space and artifact with which it serves:

| Mode | What it is | Exemplar |
|---|---|---|
| **Evocative** | Draws on the player's pre-existing narrative competencies; broad outlines they complete | Morrowind's mushroom-tree/Daedric architecture; *American McGee's Alice* |
| **Embedded** | Story as "a body of information" in the mise-en-scène — space as memory palace | A corpse beside a half-burned journal; *Myst*; FromSoftware item text |
| **Enacted** | Narrative events performed/witnessed; micronarratives along the player's path | A cliff-racer ambush on a known route; RDR2 aftermath scenes |
| **Emergent** | Spaces rich with narrative *potential*; story built by play | Levitation + theft + faction rep interacting; *The Sims* |

**Exemplar:** FromSoftware tells story through item descriptions, environment, fragmented accounts — in Elden Ring nearly every consumable/spell/status effect carries history. This scales because each artifact is a **self-contained, independently generable unit of lore**. Write descriptions as **in-world voices** (a scholar, a zealot, a peasant) that imply more than they state — Kirkbride's texts "read like artifacts of the world they inhabit," using invented terms with "allusive rather than definitive purpose."

**Test for:** ≥70% of canon a completionist can learn is reachable through diegetic artifacts, and the critical-path plot is comprehensible without reading any optional lore. Every region carries ≥1 evocative cue, ≥1 embedded clue, and ≥1 enacted micronarrative (flag regions with 0 embedded clues). Each description names/implies a *source perspective* and contains ≥1 unexplained proper noun resolving to canon.

**Failure modes:**
- **Exposition dumping** — "mechanical exposition through cut scenes" (Jenkins's caution).
- **Wallpaper world** — evocative dressing with no embedded information; looks meaningful, rewards no reading.
- **Wiki voice** — flat, omniscient, definitive text that forecloses interpretation.

> **Procedural / AI implication.** Item-description storytelling is the **most procedurally friendly technique here**: each artifact is short, bounded, generable from (entity ID, perspective, region) — concentrate generated *volume* here. Embedded narratives generate well *if* the canon graph supplies the body of information. But **evocative spaces need a hand-authored visual/asset vocabulary** — an LLM can't invent a coherent art language at runtime (→ `art-direction-and-readability`). **Emergent** narrative is a property of *systems*, not text (→ `systemic-emergent-design`). **Enacted** micronarratives need hand-authored set-piece templates (an "aftermath kit") the engine instantiates with canon-consistent specifics.

---

## 3. Identity beats fidelity — "alien but coherent"

**Rule:** Encode the world's register as a **finite, enumerable style grammar** (permitted materials, silhouettes, naming morphology, taboo tropes) and forbid generation outside it. Express the world's logic as **machine-checkable invariants** ("no iron — culture is chitin/glass"; "the dead are ancestor-revered") and validate every artifact against them. For every alien concept, anchor it to **exactly one** familiar emotional/functional handhold.

**Exemplar:** Morrowind. Kirkbride (lead world artist): "I went nuts, making everything in the game bizarre, alien, and exotic. People liked it and it stuck." Giant mushrooms, chitin armor, Dunmer naming, Daedric script form a recognizable grammar; generic-fantasy elements were consciously excluded; the team pushed to a "Bronze Age" register (RuneQuest/Glorantha, Dune, Tékumel) over Tolkien pastiche. Kirkbride's "dog story / god story" always pairs cosmic strangeness with mundane human consequence — the handhold that keeps "alien" legible.

**Test for:** every generated name/asset/place passes a style-grammar validator (phonotactic name rules; banned-trope list); flag any artifact that would also fit an off-the-shelf generic fantasy game. A contradiction-checker over the full corpus returns **zero** invariant violations before ship. Every alien noun is paired, within the same artifact or its neighbor, with a concrete sensory or human-stakes detail.

**Failure modes:**
- **Generic-fantasy regression** ("Lord of the Rings fever dream," Kirkbride's jab at Oblivion) — the model's training prior pulls everything toward Euro-medieval defaults.
- **Canon drift** — accumulation of individually-plausible additions that collectively contradict (the documented #1 risk of large-scope AI worldbuilding).
- **Opaque alienness** — strangeness with no human anchor, reading as noise.

> **Procedural / AI implication.** "Alien but coherent" is **more robust under generation than realism**, because it's an explicit grammar a validator can check — realism has an infinite implicit rule set ("does this feel like a real medieval village?") no validator can fully cover. So bias toward a strong, enumerable register. **Hand-author the seed grammar (30–50 rules) and the art vocabulary**; the model only samples within them. The biggest AI-specific risk is **prior leakage** (the base model's Tolkien/D&D prior), which must be actively suppressed via a banned-trope list and adversarial review (→ `procgen-review`, `ai-authored-content-coherence`).

---

## 4. Mythmaking & a sense of deep history

**Rule:** Make each deity/myth a **faction proxy** with mechanical consequences (a faction, a region, a set of mechanics). Author history as a **causal chain** (event X → condition Y still visible in region Z), not a timeline of names — every "ancient" claim must leave a present-day trace the player can find. Grow the iceberg **macro→micro** (Jemisin's order: cosmology → geography → peoples/migration → culture → locale → artifact), never letting a lower level contradict a higher one.

**Exemplar:** Elden Ring — the pantheon (Greater Will, Marika, demigods, Outer Gods) *is* the political faction set; status effects each belong to a god/force; the Shattering is a historical event that *causes* the present game state and the existence of the Tarnished. Morrowind's Great Houses encode "this is what these types of Dark Elves are." Jemisin's "Growing Your Iceberg" propagates one **"Element X"** (a single world-defining force) through every level.

**Test for:** every named deity resolves to ≥1 faction, ≥1 region, and ≥1 mechanic (flag decorative gods). Every historical event has ≥1 present-day environmental/item consequence (flag history nodes with zero downstream traces). The pipeline produces levels in macro→micro order, each citing its parent; "Element X" appears as a dependency at every level.

**Failure modes:**
- **Decorative pantheon** — gods that live only in flavor text and never touch play; deep history feels inert.
- **Lore calendar** — dated names with no observable consequences; players can't feel it, the engine can't surface it.
- **Bottom-up incoherence** — generating cool locales first and back-filling cosmology; guarantees contradictions at scale.

> **Procedural / AI implication (extrapolation — test it).** This is where procgen can **beat** hand-authoring: a causal graph is exactly what a machine expands consistently. **Hand-author the top ~3 levels** (cosmology, geography skeleton, founding myths, the single "Element X") — they are the roots, and an LLM inventing them per-session is the surest route to drift. Then let the engine propagate consequences downward, each node required to cite a parent and surface ≥1 present-day trace. The pantheon-as-faction-proxy pattern is the bridge to §5: it turns mythology into mechanics.

---

## 5. Ludonarrative harmony — mechanics that *are* the story

**Rule:** For each core mechanic, write its **ludic contract** and its **narrative contract** as one sentence each. If they conflict, either resolve it or make the conflict the explicit theme — never ship an *unintended* mismatch. No mechanic ships without an in-world explanation; tie each system (loot, death, economy, status) to canon. Treat constraints as **theme generators**, not friction.

**Exemplar:** Clint Hocking's "Ludonarrative Dissonance in BioShock" (2007): the ludic contract ("seek power and progress") and narrative contract ("obey Atlas and progress") disagree, and the game "openly mocks us for having willingly suspended our disbelief." The constructive inverse — *Spec Ops: The Line* (intended complicity, Walt Williams GDC 2013), *Papers, Please* (bureaucratic complicity is the verb), *Pathologic 2* (punishing controls simulate illness). Elden Ring: "almost every one of the myriad mechanics has a direct tie to the narrative." Sanderson's Second Law — **limitations are more interesting than powers.**

**For the running example (Valenfeld):**
- **Finite legendary loot** = a world whose age of wonders is *ending* (scarcity is lore, not a balance lever) — see `rpg-systems` (finite-legendary manifest).
- **Single-save permadeath** = mortality is real and consequential — see `permadeath-and-lethality` (and its diegetic-continuity work).
- **The token economy** = an in-world *institution* with a history the engine *references*, never re-invents.

**Test for:** every core mechanic has a documented (ludic, narrative) contract pair + a flag `{ALIGNED | INTENTIONAL_TENSION}`; zero flagged `UNRESOLVED` at ship. Each mechanic links to ≥1 canon entity explaining *why it works that way* (flag "gamey" systems — respawn, fast travel — with no diegetic account). Each constraint maps to a stated theme AND a canon institution/event; removing it would falsify a theme. Every non-diegetic UI element is logged with a diegetic justification or an explicit "accessibility exception" tag.

**Failure modes:**
- **Ludonarrative dissonance** — gameplay argues one theme while the story asserts the opposite.
- **Gamey abstraction** — systems that exist only as balance levers, signaling the world is a façade.
- **Arbitrary difficulty** — constraints that read as developer hostility because no narrative justifies them.
- **HUD-driven world bypass** — quest markers let players complete content while skipping all lore, hollowing the iceberg from the player's side (Morrowind's no-marker, follow-the-directions design forces world-reading — at a real, documented accessibility cost).

> **Procedural / AI implication.** Harmony is **not** a per-artifact generation problem — it's a design-time invariant between systems and canon. Encode it as a **machine-checked mechanic↔lore mapping table** the agent maintains, and treat "mechanic with no canon link" as a **build-breaking error**. For Valenfeld: because loot is *finite*, the generator must keep a **global ledger of legendary items as canon** (each with a unique history) and may never mint duplicates — that breaks balance *and* lore at once.

---

## 6. Caveats (carried from the source synthesis)

- **Translation risk (Miyazaki).** Quotes reach English via interpreters/outlets; the PlayStation Blog (E3 2018) "fill in the gaps" wording is verbatim, but treat other-outlet phrasings as faithful-in-substance, not word-perfect.
- **Contested theory (Hocking).** He framed BioShock's dissonance as a flaw; substantial later criticism (and the "would you kindly" twist) argues it's intentional and thematically productive. Use the concept; don't treat "dissonance = bad" as settled.
- **Fan-interpretation vs. dev-confirmed (FromSoftware).** Mechanics↔lore ties cited here are dev-authored item texts; broader narrative syntheses are community reconstruction — flag as such if reused.
- **Kirkbride's status.** His art-direction *intent* for Morrowind is well documented, but much of his most-cited lore (C0DA, "MK" forum posts) is explicitly non-canonical/personal — separate it from shipped Morrowind content.
- **Prose-craft, not game design.** Sanderson/Jemisin/Hemingway transfer well to *authored* worldbuilding but weren't written for interactive/systemic/procedural media; Jenkins and Hocking are the game-native correctives.
- **RDR2 is a partial procedural exemplar.** Its "lived-in" density is overwhelmingly hand-authored at enormous cost (~2,000 staff; hundreds of millions). Cite it for the *target feel* of environmental/aftermath storytelling, not as evidence such density is cheaply procedural.
- **AI-authoring risks the sources don't cover.** None of these sources (1932–2018) anticipated an LLM author. The frozen-canon / generate-leaves / validators-first mitigations are extrapolations — engineering hypotheses to test, not received practice.

---

## Recommendations (staged)

**Stage 0 — Author the roots (hand-authored, before any generation).**
1. Write the canon backbone as typed data: cosmology, the single "Element X," founding myths, the pantheon-as-factions table, geography skeleton, timeline-as-causal-graph. **Freeze it.**
2. Author the style grammar (30–50 enumerable rules) and the art/asset vocabulary that encodes the "alien but coherent" register.
3. Build the mechanic↔lore mapping table: one row per core system, each with ludic + narrative contracts, an alignment flag, and a linked canon entity.

**Stage 1 — Build the validators before the generators.**
4. Contradiction-checker (rejects any artifact referencing a non-existent entity or violating an invariant).
5. Style-grammar validator (names, assets, banned tropes) + a prior-leakage adversarial check for generic-fantasy regression.
6. Coverage checker (Jenkins-mode tagging per region; macro→micro citation order).

**Stage 2 — Generate leaves, never roots.**
7. High volume only where safe: item descriptions + embedded clues from (entity ID, perspective, region), ≤ word cap, allusive voice, ≥1 unexplained proper noun resolving to canon.
8. Instantiate enacted micronarratives from hand-authored set-piece templates.
9. Propagate history downward — each node cites a parent and surfaces ≥1 present-day trace.

**Stage 3 — Gate, playtest, measure the iceberg.**
10. Every artifact through all validators; nothing ships un-gated (→ `procgen-review`).
11. Confirm critical-path comprehension without optional lore, and ≥70% of canon reachable diegetically.
12. Maintain the finite-loot ledger as canon; assert uniqueness on every legendary mint.

**Thresholds that change the plan:**
- Contradiction-checker flags **>~2%** of artifacts → stop scaling; shrink/clarify the canon graph (drift is outrunning your invariants).
- Playtesters can't state the critical-path plot without optional lore → below the safe waterline on enacted/embedded delivery; add diegetic anchors.
- **>~30%** of perceived gaps lack ≥2 anchors → ambiguity is unbounded; convert `UNDECIDED` → `HIDDEN_BUT_KNOWABLE` and place anchors.
- Generic-fantasy regression rate climbs → the prior is winning; tighten banned tropes + adversarial review before adding any new content type.
- Any core mechanic still `UNRESOLVED` in the mapping table → build-breaker, not polish.
- Finite-loot ledger mints a duplicate legendary → **halt** (the generator is writing to roots).

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Hollow iceberg | Gaps that read as contradiction/emptiness | Frozen typed canon before any surface artifact (§1) |
| Info-dump | Front-loaded encyclopedia, dead pacing | Reveal budget + smallest-artifact rule (§1) |
| Unbounded ambiguity | Gaps with no anchors | ≥2 anchored facts around every perceptible gap (§1) |
| Exposition dumping | Lore via cutscene/dialogue by default | Default to item/environment/aftermath (§2) |
| Wallpaper world | Evocative dressing, 0 embedded clues | ≥1 embedded clue per region (§2) |
| Wiki voice | Flat omniscient definitive text | In-world voice + unexplained proper noun (§2) |
| Generic-fantasy regression | Could be any D&D game | Enumerable style grammar + banned tropes (§3) |
| Canon drift | Plausible additions that collectively contradict | Machine-checked invariants over frozen canon (§3) |
| Decorative pantheon | Gods only in flavor text | Deity → faction + region + mechanic (§4) |
| Lore calendar | Dated names, no traces | History as causal chain w/ present-day trace (§4) |
| Ludonarrative dissonance | Mechanic argues against the story | (ludic, narrative) contract pair per mechanic (§5) |
| Gamey abstraction | Systems with no diegetic account | Every system links to canon (§5) |
| HUD world bypass | Players finish content, skip all lore | Diegetic delivery; justify every marker (§5) |

---

*Distilled from a deep-research synthesis (Hemingway, Jenkins, Sanderson, Jemisin, Hocking; Morrowind/FromSoftware/Elden Ring/RDR2). Craft sources predate LLM authoring; the frozen-canon / generate-leaves / validators-first program is the AI-native extrapolation and should be validated in a generation loop. See `CONTRIBUTING.md` and `docs/research-prompts.md` §6.*
