# AI-Authored Content Coherence — Guide

Keeping AI-/procedurally-authored content coherent at scale: one voice, no retcons, generated figures that cohere into arcs instead of dissolving into oatmeal. Pair with `CHECKLIST.md` for the actionable version.

> **The central law:** *mathematical uniqueness ≠ perceived variety.* Win coherence with a shared voice, a never-violate fact bible, recurring thematic domains, and an explicit self-review pass — and accept that coherence at full open-world scale is **unsolved**, so de-risk with the oatmeal/fanfic tests early.

> **Verification status (read this — it matters most here).** This topic's deep-research verification phase largely **failed** (session limit), and the synthesis step partly failed too. Almost every rule below is **⚠️ sourced but unverified**: a real, strong source was fetched and the claim extracted, but adversarial verification did not complete. The *sources* are reputable (Kate Compton; Grinblat & Bucklew / Caves of Qud; peer-reviewed LLM-consistency work). Treat each rule as a well-cited engineering hypothesis to validate in your own pipeline, not confirmed fact. Tags: ✅ verified · ⚠️ sourced-unverified · ❌ refuted.

---

## 1. The oatmeal problem is the core risk ⚠️

**Rule:** Measure generated content by **perceptual uniqueness**, not combinatorial count; assume mathematically-distinct output can still read as one undifferentiated mass.

**Exemplar / source:** Kate Compton's "10,000 Bowls of Oatmeal" — "I can easily generate 10,000 bowls of oatmeal, each unique… but the user will likely just see a lot of oatmeal." A generator can produce countless mathematically-distinct artifacts that users perceive as a single mass. (Sources: galaxykate "So you want to build a generator"; Compton oatmeal PDF.)

**Test for:** sample N generated artifacts; can a fresh reader tell them apart *and* remember any individually? If they blur together, the generator is producing oatmeal regardless of its distinctness math. (This is the `procgen-review` oatmeal test.)

**Anti-pattern:** *banal uniformity* — shipping volume as if count were variety; the signature failure of mass AI-generation.

---

## 2. Two quality bars: differentiation vs. uniqueness ⚠️

**Rule:** Distinguish the **easy bar** (*perceptual differentiation* — this artifact isn't identical to the last) from the **hard bar** (*perceptual uniqueness* — this artifact is memorable as a distinct "character"); demand uniqueness only where it pays.

**Exemplar / source:** Compton — the two bars are "separately achievable"; differentiation satisfies a baseline aesthetic need, uniqueness is much harder and **not required of every artifact**. (Source: galaxykate, oatmeal PDF.)

**Test for:** every artifact clears differentiation; *hero* artifacts (legendary loot, named figures, landmark events) additionally clear uniqueness. Budget the expensive bar deliberately.

**Anti-pattern:** *uniqueness everywhere* — spending the hard bar on filler (unsustainable), or *differentiation nowhere* — even hero content blurs.

---

## 3. Single voice via a curated corpus + style spec ⚠️

**Rule:** Anchor voice to a **curated, hand-authored corpus** and codify its diction into a reusable spec the generator must conform to.

**Exemplar / source:** *Caves of Qud* achieves single-voice consistency at scale via a curated corpus (reported ~40,000 words) whose diction is codified into a Tracery-like replacement grammar stored as static JSON, so generated text reuses the game's established voice. For an LLM engine, the analogue is a **style/voice spec plus exemplars** in the prompt/constraints. (Source: Grinblat & Bucklew, FDG'17, "Subverting Historical Cause & Effect.") *This is the textual analogue of the named style-bible in `art-direction-and-readability` §6.*

**Test for:** a held-out reader can't distinguish generated passages from hand-authored corpus passages on voice; new diction outside the spec is flagged.

**Anti-pattern:** *voice drift* — each generation in a slightly different register, so the world has no narrator.

---

## 4. Generate-then-rationalize causation ⚠️

**Rule:** For history/biography, generate **events first** and rationalize a cause **after the fact** from the figure's existing state — don't run a full causal simulation.

**Exemplar / source:** *Caves of Qud*'s history generator picks an event at random, then generates/justifies a cause ex post facto from the sultan's state (it can even invent the cause by mutating state), so **causality is player-inferred, not simulated.** (Sources: Grinblat & Bucklew FDG'17; GDC 2018 "Procedurally Generating History in Caves of Qud." This is the one claim with a partial verifier signal, 1-0 — still treat as ⚠️.)

**Test for:** generated histories read as causally connected to a reader, while the engine stores only events + a small state pool (no full simulation graph).

**Anti-pattern:** *simulation tax* — building an expensive cause-and-effect simulator when post-hoc rationalization over shared state would read the same.

---

## 5. Recurring thematic domains turn events into arcs ⚠️

**Rule:** Parameterize generated figures/events off a **small shared pool of properties**, especially an assigned archetypal **"domain"** (e.g. ice / glass / might), so disjoint random events cohere into a recognizable arc.

**Exemplar / source:** *Caves of Qud* — coherence across randomly-chosen events comes not from causal logic but from every event reading off a small shared property pool; the assigned domain acts as a recurring thread tying a figure's life into an arc. (Source: Grinblat & Bucklew FDG'17.)

**Test for:** each generated figure has 1–3 recurring threads (domain/motif/relationship) that surface across its events; a reader can name "what this figure is about."

**Anti-pattern:** *thread-less generation* — each event independently random, so a figure is a list of unrelated incidents with no through-line.

---

## 6. Apophenia by design ⚠️

**Rule:** Generate **evocative, not exhaustively-detailed** content and let the player's surrounding context supply meaning; don't over-prescribe the narrative arc.

**Exemplar / source:** *Caves of Qud* deliberately designs for apophenia — evocative biographies, no prescribed arc — relying on the player to interpret meaning from surrounding narrative context. Compton's complement: artifacts read as meaningful when they show **"evidence of process and forces"** (implied causation), making the world feel alive and intentional. (Sources: Grinblat & Bucklew FDG'17; galaxykate "build a generator.")

**Test for:** generated artifacts imply more than they state (a hook, a gap, an implied cause) without contradicting the bible; players report inventing connections.

**Anti-pattern:** *over-specification* — exhaustive generated detail that leaves no room for inference and magnifies any contradiction.

---

## 7. A machine-readable lore bible with a never-violate list ⚠️

**Rule:** Maintain the lore bible as a **machine-readable constraint set** with an explicit **"never violate"** list, and track each generated fact as a **directional atomic fact with a time-bounded validity interval** so contradictions against accumulated world state are detectable.

**Exemplar / source:** the topic brief's fact-checking-grid concept, plus the LLM-consistency literature: tracking each fact with an explicit time window (a "directional atomic fact" plus when it holds) detects contradictions better than re-feeding raw context to the same model. (Source: arXiv:2407.16347.) Constrain new generation to reference an **already-established context pool** (Qud constrains references to its period-by-period history).

**Test for:** every generated fact is checked against the never-violate list and the accumulated fact store before commit; no new fact contradicts an existing fact within an overlapping validity interval.

**Anti-pattern:** *retcon drift* — later generation silently contradicting established facts because nothing machine-checks against the bible.

---

## 8. Static backbone + procedural tissue ⚠️

**Rule:** Hand-author a static **backbone** (the never-violate spine: major places, powers, eras, the legendary set) and let the generator fill **tissue** around it; keep the ratio conservative until the self-review pass proves the generator holds coherence.

**Exemplar / source:** the hybrid handcrafted-anchor + constrained-fill pattern (see `procedural-generation`); Qud's static-corpus-plus-generated-history is one instance. (Topic synthesis; cross-linked to the verified `procedural-generation` skill.)

**Test for:** every generated region/figure/item attaches to at least one backbone anchor; nothing story-critical is purely generated.

**Anti-pattern:** *all-tissue* — generating the spine itself, so there's nothing fixed for coherence to hang on.

---

## 9. The explicit self-review pass (→ `procgen-review`) ⚠️

**Rule:** Make a **critic an architectural component, not an optional add-on**: every generated batch passes a self-review that compares new instances against prior ones, flags duplication and contradiction, and returns accept / reject / repair to the generator.

**Exemplar / source:** Compton & Mateas on generative methods — a generative method "should include a critic that validates the proposed artifact and feeds an accept/reject decision, reward, or corrected artifact back to the generator"; critics can be **human as well as computational**, and humans uniquely supply aesthetic heuristics (beauty, uniqueness, interestingness). The LLM-consistency work shows automated contradiction-detection over long narratives is feasible and **error-grounded in textual evidence** (a "ConStory-Checker"-style pipeline), and that consistency errors **cluster in factual/temporal dimensions around the middle of narratives** — so weight mid-story fact/timeline checks most heavily. (Sources: ComptonOsbornMateas "Generative Methods"; arXiv:2603.05890.)

**Test for:** no batch commits without a self-review verdict; the pass runs the oatmeal test (§1), the never-violate check (§7), a cross-instance duplication scan, and a mid-narrative fact/timeline check. This pass *is* the `procgen-review` gate.

**Anti-pattern:** *generate-and-ship* — committing generated content with no critic, so sameness and contradictions surface only in front of players.

---

## 10. Rarity and value (why finite still matters) ⚠️

**Rule:** Protect rarity deliberately — an unbounded supply of at-or-above-quality content **destroys the value** of any single piece, so keep prestige content **finite and hand-authored**.

**Exemplar / source:** the "Bach faucet" argument — a generator that endlessly produces content at/above a culturally-valued original makes that content no longer rare and thus less valuable; a generated artifact can't give the same experience as a handcrafted one because the player *knows another can be generated*. This directly justifies **Valenfeld's finite legendary loot and hand-authored backbone** over infinitely-regeneratable items. (Source: ACM FDG 2023, abs/3582437.3587212.)

**Test for:** prestige artifacts (legendary loot, named NPCs, capstone events) are finite and not regeneratable; the generator can't mint more of them on demand.

**Anti-pattern:** *infinite legendaries* — generating prestige content on demand, collapsing its value.

---

## The honest caveat (do not skip)

Narrative coherence at **full open-world scale is unsolved**, and this skill's evidence base is the weakest in the pack because verification didn't complete. Two compounding AI-authoring risks the source literature does *not* cover: (1) LLMs systematically contradict their own established facts over long output — consistency is a *measurable failure mode*, not an occasional glitch; (2) the self-review critic (§9) is itself an LLM and can miss or hallucinate contradictions. **Mitigation:** de-risk from day one — run the oatmeal test (§1) and the fanfic/retell test (`procgen-review`) on small batches before scaling, keep the static backbone large (§8) and rarity finite (§10), and re-run a clean verification pass on this topic before treating any rule here as settled.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Banal uniformity | Volume mistaken for variety | Measure perceptual uniqueness (§1) |
| Uniqueness everywhere | Hard bar spent on filler | Two-bar budget (§2) |
| Voice drift | No consistent narrator | Curated corpus + style spec (§3) |
| Simulation tax | Expensive causal sim | Generate-then-rationalize (§4) |
| Thread-less generation | Figures = unrelated incidents | Recurring thematic domains (§5) |
| Over-specification | No room to infer; contradictions magnified | Apophenia by design (§6) |
| Retcon drift | New facts contradict old | Machine-readable never-violate bible (§7) |
| All-tissue | The spine itself is generated | Static backbone + tissue (§8) |
| Generate-and-ship | No critic before commit | Self-review pass / `procgen-review` (§9) |
| Infinite legendaries | Prestige content on demand | Finite, hand-authored rarity (§10) |

---

*Almost every rule here is sourced-but-unverified — re-run a clean verification pass before treating as settled. Open questions: does each Qud technique transfer from a 2D roguelike to a full 3D open world? Do LLM-consistency pipelines hold at game scale? See CONTRIBUTING.md.*
