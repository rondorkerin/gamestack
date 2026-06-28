# Art Direction & Readability — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for visual communication. Run these against a scene, a character, or a generated asset. See `GUIDE.md` for reasoning and sources. Items tagged ✅ rest on verified TF2 primary sources; ⚠️ are sourced but await re-verification.

---

## Readability as objective ✅

**Do**
- [ ] Make every visual decision serve a **named gameplay read** before taste.
- [ ] Attach a **read contract** to each element (what gameplay question it answers).

**Don't**
- [ ] Don't ship decoration-first art — pretty elements that answer no gameplay question.

**Test for** — Can every visual element name the gameplay question it answers? Flag the ones that can't.

---

## Silhouette ✅

**Do**
- [ ] Design and validate every character/creature/landmark as a **flat silhouette first**.
- [ ] Re-verify by converting the finished asset back to flat black/gray.

**Don't**
- [ ] Don't rely on texture/color to distinguish entities (silhouette collision).

**Test for** — Render each entity as a single-color silhouette: is it distinguishable from every other in-tier silhouette without internal shading?

---

## Value & contrast ✅

**Do**
- [ ] Grade value so **peak local contrast lands on the most decision-relevant element**.
- [ ] Keep surrounding areas lower-contrast so the signal pops.

**Don't**
- [ ] Don't make uniformly high-contrast scenes where the eye has no anchor.

**Test for** — Does the region of maximum luminance contrast coincide with the highest-priority gameplay element? If it lands on background/decoration, flag.

---

## Signal color ✅ (registry) / ⚠️ (case studies)

**Do**
- [ ] Give each reserved signal color **exactly one meaning**, tied to the most urgent decision. ✅
- [ ] Offload secondary distinctions to **silhouette/proportion**, not more colors. ✅
- [ ] Keep a small **reserved-color registry**; ensure each contrasts in every biome (see `open-world-design` §7). ✅
- [ ] If you use a navigation/interaction color (red/yellow), keep it diegetic and single-purpose. ⚠️

**Don't**
- [ ] Don't reuse a signal color for a second meaning, or paint it on non-signal decoration (dilution). ✅
- [ ] Don't use signal paint to patch a scene that fidelity creep made illegible (paint-as-crutch). ⚠️

**Test for** — Does each signal color map to exactly one documented meaning? Is the urgent decision (friend/foe-equivalent) answerable from color alone?

---

## Fidelity vs. readability ✅

**Do**
- [ ] **Cap background detail/texture frequency** where it would compete with signal elements.
- [ ] Prefer broad, controlled value/color fields over uniform high-frequency noise.

**Don't**
- [ ] Don't let fidelity creep push interactive elements into the visual noise floor.

**Test for** — Do gameplay-signal elements keep a measurable contrast/frequency margin above their surroundings?

---

## House style ✅

**Do**
- [ ] Pick a **concrete, nameable reference set** up front (e.g. named illustrators/films/palette).
- [ ] Write a style bible and **score each generated asset for conformance**.

**Don't**
- [ ] Don't define "stylized" only as "not realistic" — that's style drift.

**Test for** — Is there a written, specific style bible, and can each asset be scored against it?

---

## Stylization preserves cues ⚠️

**Do**
- [ ] Ensure the style still lets players **predict system behavior from appearance** (flammable looks flammable; climbable looks climbable).

**Don't**
- [ ] Don't abstract so far that players can't infer mechanics from looks.

**Test for** — Can a player predict what a thing does from how it looks, in this style?

---

## Procgen readability gates (the crux)

**Do**
- [ ] Enforce a **per-asset read contract** at generation time; flag empty contracts.
- [ ] Run a **silhouette-uniqueness gate** per tier before commit; route failures back to the generator.
- [ ] Enforce **peak-contrast-on-signal** as a computable constraint.
- [ ] Forbid the generator from emitting a **reserved color** on non-signal assets.
- [ ] Measure every generated asset against the **named style bible**.

**Don't**
- [ ] Don't assume per-asset readability composes into coherence at scale — it's unproven.

**Test for (the key one)** — For a fresh batch of N generated assets: do they pass silhouette-distinctness, read-contract, and style-conformance, *and* still read as one coherent art direction when viewed together? (Run with `procgen-review`.)
