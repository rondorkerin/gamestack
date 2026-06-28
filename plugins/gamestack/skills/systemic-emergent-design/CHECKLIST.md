# Systemic-Emergent Design — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for building an emergent rule substrate and generating content over it. Run these against a systems spec or a batch of generated regions/encounters. Test-for items are written to be enforced as automated gates in a headless loop. See `GUIDE.md` for reasoning and sources.

---

## Universal rules & verbs (immersive-sim substrate)

**Do**
- [ ] Express every interactive behavior as `{material tags} × {global rules}` — universal classes (flammable, conductive, climbable), not per-object scripts.
- [ ] For each core verb, specify **what plan it lets the player form** and **the immediate, perceivable world reaction**.

**Don't**
- [ ] Don't hand-script one-off interactions on single objects (the Fabergé Object).
- [ ] Don't ship consequences the player can't perceive within one action-feedback loop (the Invisible Lever).

**Test for** — Can every interactive property be expressed via tags × rules (zero object-unique scripts)? Does every verb have a perceivable consequence within one feedback loop?

---

## Interaction density & system depth

**Do**
- [ ] Build **few deep systems** and maximize **interactions between** them; aim for I scaling toward S².
- [ ] Make each system **stateful and persistent** — its output is another system's input, so chains form.

**Don't**
- [ ] Don't ship many siloed "systems" connected only by scripts (the Costume Box).
- [ ] Don't ship momentary interactions that leave no state behind (the Mayfly System).

**Test for** — Does interaction count scale toward S² (most systems can affect most others)? Does every core system expose persistent state ≥1 other system reads *and* mutates?

---

## Multi-solution gates & lawful resolution (the GM analogy)

**Do**
- [ ] Simulate each obstacle deeply enough that **≥2 systemic solutions** exist (not 2 scripted branches).
- [ ] Make out-of-distribution player actions resolve to a **lawful, rule-consistent result**.
- [ ] Use AI authoring for *content* (situations, factions, motivations); keep the rule substrate frozen.

**Don't**
- [ ] Don't gate progress on a single intended key (the Telekinesis Lock) unless explicitly marked a hard-authored bottleneck.
- [ ] Don't let reasonable unanticipated actions hit a silent null/locked state (the Empty GM Chair).

**Test for** — Does every gate have ≥2 systemic solutions (or an explicit bottleneck tag)? Do sampled out-of-distribution actions produce lawful results, never "nothing happens"?

---

## Multiplicative, not additive

**Do**
- [ ] Keep **capabilities and world properties in separate tables**; let the product space be the gameplay.
- [ ] Map each base rule to a **one-sentence real-world intuition** so it's learnable by exposure.
- [ ] Add a system only if it **reads from and writes to ≥2 existing systems**.

**Don't**
- [ ] Don't enumerate "approved combinations" as content (the Recipe Book).
- [ ] Don't ship rules that defy real-world intuition (the Arbitrary Alchemy).
- [ ] Don't bolt on features that interact with nothing else (the Bolt-On).

**Test for** — Are capabilities × properties left to combine freely (no enumerated combo list)? Does each rule map to a real-world intuition? Does every new system touch ≥2 existing ones?

---

## Emergence × procedural generation

**Do**
- [ ] Generate **inputs to systems**, not finished experiences — each generated class is consumed by ≥1 system that changes player options.
- [ ] **Freeze a versioned, read-only rule substrate before generating**; generate only rule-compliant artifacts.
- [ ] Tune generation for **systemic salience** — make instances *matter* differently, not just look different.

**Don't**
- [ ] Don't generate cosmetic content no system reacts to (the Oatmeal Bowl).
- [ ] Don't let any generator code path mutate the rule set (the Shifting Physics) — critical violation.
- [ ] Don't ship structurally identical generated quests (the Radiant Quest Trap).

**Test for** — Is every generated content class consumed by ≥1 system? Does the generator's I/O contract prove it consumes a read-only rule set and never mutates it? Can a blind proxy name a *consequential* (not cosmetic) difference between two instances?

---

## Headless guardrails (author once, enforce always)

These are the **hand-authored invariants** the generator must respect — declare them before generating, validate every batch, and **block the build** on failure:

- [ ] **The material-tag taxonomy + global rule table** ("chemistry engine"), near-exceptionless and real-world-mapped, **versioned read-only** (4.1, 4.2, 5.2).
- [ ] **Per-verb Intention + Perceivable Consequence** spec (1.2).
- [ ] **Interaction-density target** (I/S²) and a per-system "reads+mutates ≥2 others" rule (2.1, 2.2, 4.3).
- [ ] **Multi-solution gate check** (≥2 systemic paths) and **out-of-distribution lawful-resolution fuzzer** (3.1, 3.2).
- [ ] **Undesirable-emergence fuzzing** — enumerate systemic combinations that trivialize gates or break conservation invariants (finite-loot cap, token-economy chains), and bound them.
- [ ] **The signature-mechanic invariants** as hard constraints: finite-loot global cap, lethality-requires-telegraphing, tokens-as-input-to-≥2-systems (5.3 synthesis; cross-check `rpg-systems`, `combat-design`).

> Run the **Juul test** (strategy guide vs. walkthrough) and the **oatmeal test** (consequential vs. cosmetic difference) on generated regions, and gate every batch through `procgen-review`. Thresholds that change the plan: if interaction density I/S² is low (systems siloed), *stop generating content and go back to building systems* — you have a progression game, not an emergent one.
