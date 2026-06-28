# AI-Authored Content Coherence — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for keeping generated content coherent at scale. Run against a generator, a corpus, and a self-review pass. See `GUIDE.md` for reasoning and sources.

> ⚠️ **Almost everything here is sourced-but-unverified** (this topic's verification phase failed on a session limit). Strong, well-cited defaults — validate in your own pipeline before treating as law.

---

## Sameness (the oatmeal problem)

**Do**
- [ ] Measure content by **perceptual uniqueness**, not combinatorial count.
- [ ] Clear the **differentiation** bar for everything; clear the **uniqueness** bar for hero content only.

**Don't**
- [ ] Don't ship volume as if count were variety (banal uniformity).
- [ ] Don't spend the expensive uniqueness bar on filler.

**Test for** — Sample N artifacts: can a fresh reader tell them apart *and* remember any? (The `procgen-review` oatmeal test.)

---

## Single voice

**Do**
- [ ] Anchor voice to a **curated hand-authored corpus** + a codified style/voice spec with exemplars.
- [ ] Make the generator **conform to the spec**; flag diction outside it.

**Don't**
- [ ] Don't let each generation drift into a different register.

**Test for** — Can a held-out reader distinguish generated passages from corpus passages on voice?

---

## Causation & arcs

**Do**
- [ ] **Generate events first, rationalize cause after** from a small shared state pool.
- [ ] Give each figure **1–3 recurring threads** (an archetypal "domain"/motif) that surface across events.

**Don't**
- [ ] Don't build an expensive full causal simulation when post-hoc rationalization reads the same.
- [ ] Don't emit figures as lists of unrelated random incidents (thread-less).

**Test for** — Do histories read as causally connected? Can a reader name "what this figure is about"?

---

## Apophenia by design

**Do**
- [ ] Generate **evocative, not exhaustive** content; imply process and forces.
- [ ] Leave gaps for the player to interpret.

**Don't**
- [ ] Don't over-specify — it removes inference and magnifies contradictions.

**Test for** — Do artifacts imply more than they state (hook/gap/implied cause) without contradicting the bible?

---

## Lore bible / never-violate

**Do**
- [ ] Keep the bible **machine-readable** with an explicit **never-violate** list.
- [ ] Track each generated fact as a **directional atomic fact with a validity time-window**.
- [ ] Constrain new generation to reference an **already-established context pool**.

**Don't**
- [ ] Don't let later generation silently contradict established facts (retcon drift).

**Test for** — Is every new fact checked against the never-violate list and accumulated store before commit? Any contradiction within overlapping validity intervals?

---

## Backbone vs. tissue

**Do**
- [ ] Hand-author the **static backbone** (places, powers, eras, legendary set).
- [ ] Attach every generated region/figure/item to **≥1 backbone anchor**.

**Don't**
- [ ] Don't generate the spine itself (all-tissue).

**Test for** — Is anything story-critical purely generated? (It shouldn't be.)

---

## Self-review pass (= `procgen-review`)

**Do**
- [ ] Make a **critic an architectural component**: accept / reject / repair per batch.
- [ ] Run oatmeal test + never-violate check + cross-instance duplication scan + **mid-narrative fact/timeline check** (errors cluster mid-story).
- [ ] Use a **human critic** for aesthetic heuristics the computational critic can't supply.

**Don't**
- [ ] Don't generate-and-ship with no critic.
- [ ] Don't fully trust the LLM critic — it can miss/hallucinate contradictions.

**Test for** — Does any batch commit without a self-review verdict? (It shouldn't.)

---

## Rarity

**Do**
- [ ] Keep prestige content (legendary loot, named NPCs, capstone events) **finite and hand-authored**.

**Don't**
- [ ] Don't let the generator mint prestige content on demand (infinite legendaries collapse value).

**Test for** — Can the generator produce more legendaries/named figures on demand? (It shouldn't.)

---

## De-risk early (the meta-check)

**Do**
- [ ] Run the **oatmeal + fanfic/retell tests on small batches before scaling**.
- [ ] Re-run a **clean verification pass** on this topic before treating any rule as settled.

**Test for (the key one)** — Before scaling generation: has a small batch passed oatmeal, never-violate, and fanfic/retell gates? Coherence at full open-world scale is **unsolved** — prove it small first.
