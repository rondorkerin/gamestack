# Procedural Generation — Checklist

Actionable **Do / Don't** + **Test-for** criteria for designing a generator. See `GUIDE.md` for reasoning; gate output with `procgen-review`.

---

## Before you build the generator

**Do**
- [ ] Write down the concrete properties of a **good** artifact and a **bad** one.
- [ ] List the **hard constraints** — things that must *never* be generated. Star them.
- [ ] Decide the **handcrafted:procedural ratio** and what the hand-authored anchors are.

**Don't**
- [ ] Don't start generating before "good" is defined — you'll only produce occasionally-acceptable noise.

**Test for** — Can you describe a passing artifact as a set of properties? Is the "never" list explicit?

---

## Hybrid model

**Do**
- [ ] Hand-author a **static backbone** (finite legendary loot, named landmarks, mythic spine, key quests).
- [ ] Proceduralize only the **connective tissue**.
- [ ] Build from handcrafted **templates/chunks**, assembled procedurally (Spelunky model).
- [ ] Run a **touch-up / repair pass** on generated drafts.

**Don't**
- [ ] Don't ship pure unconstrained generation.
- [ ] Don't leave any generated area without ≥1 hand-authored anchor nearby.

**Test for** — Is every generated area anchored? Are there per-biome generation rules?

---

## Voice & canon coherence

**Do**
- [ ] Generate all text from a **voice-consistent corpus / style spec** so it shares one identity.
- [ ] Let generated content reference **only already-established canon** (a context pool).
- [ ] Generate events, then **rationalize** them against existing facts; give figures recurring **domains**.

**Don't**
- [ ] Don't let generators invent facts that contradict canon.
- [ ] Don't let generated text stick out as a different "voice."

**Test for** — Could a player tell generated text from hand-written text? Does any generated fact contradict canon?

---

## Intentionality & local logic

**Do**
- [ ] Make each location answer **who built this, what happened here, why is this reward here**.
- [ ] Give rewards that are **thematically tied** to the location type.
- [ ] Connect each location **logically to its neighbors**.
- [ ] Give each instance a **clean completion arc**.

**Don't**
- [ ] Don't generate geometry with no backstory.
- [ ] Don't place rewards disconnected from context.
- [ ] Don't generate locations that ignore their surroundings.

**Test for** — Does each location answer who/what/why? Are rewards location-appropriate? Is there a completion arc?

---

## Multiplicative systems

**Do**
- [ ] Define a **small set of properties/rules that combine** (chemistry/physics ruleset).
- [ ] Author **affordances, not solutions**; let emergence do the combinatorial work.
- [ ] Aim for content with **multiple valid approaches**.

**Don't**
- [ ] Don't hand-script a single solution per obstacle.
- [ ] Don't build systems that can't interact.

**Test for** — Do systems combine into solutions you didn't script? Does a sample obstacle have ≥2 solutions?

---

## Curated randomness

**Do**
- [ ] Use **weighted distributions** and **draw-without-replacement** to avoid clumps/repeats.
- [ ] Use **barnacling** (medium around large, small around medium) for natural composition.
- [ ] Tie generation to **mechanics** — generate things that matter, not just cosmetics.

**Don't**
- [ ] Don't lean on raw uniform random.
- [ ] Don't ship purely decorative procgen (players see through it fast).

**Test for** — Are repeats throttled? Does generated content correlate with gameplay?

---

## The standing tests (run via `procgen-review`)

**Do**
- [ ] **Oatmeal test** — sample blind; outputs must be tellable apart.
- [ ] **Fanfic/retell test** — outputs should be worth retelling; if not, add systemic depth or anchors.
- [ ] **Cross-instance sameness scan** — compare each new instance against prior ones; flag duplication.

**Don't**
- [ ] Don't commit generated content that hasn't passed these.

**Test for** — Would the 11th instance still be worth visiting? Would a player retell it?
