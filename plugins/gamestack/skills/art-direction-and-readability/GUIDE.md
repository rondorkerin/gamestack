# Art Direction & Readability — Guide

The design discipline of visual communication: how stylization, silhouette, value, and color make a game *read*, and how an AI engine holds a coherent house style across generated content. Pair with `CHECKLIST.md` for the actionable version.

> **The central law:** readability is a deliberate, engineerable objective subordinate to gameplay — *readability first, personality second, fidelity never.*

> **Verification status (read this).** This guide came from a deep-research pass whose verification phase was truncated by a session limit. Each rule is tagged:
> - ✅ **Verified** — survived 3-of-3 adversarial verification against a primary source.
> - ⚠️ **Sourced, unverified** — a real source exists and was fetched, but verification didn't complete; treat as a strong default to re-check, not settled fact.
> - ❌ **Refuted / do-not-encode** — actively failed verification; do not write as a rule.
>
> The TF2-grounded core (§1–6) is ✅. The signal-color case studies (§7) are ⚠️.

---

## 1. Readability is a first-class design objective ✅

**Rule:** Make every visual decision serve a named gameplay read *before* it serves taste; treat readability as a spec requirement, not an aesthetic afterthought.

**Exemplar:** *Team Fortress 2.* Valve's GDC 2008 talk *"Stylization With a Purpose"* lists the visual style's three functional goals verbatim — **"Gameplay, Readability, Branding."** The NPAR 2007 paper *"Illustrative Rendering in Team Fortress 2"* states the shading is "designed to quickly convey geometric information… so that game players are consistently able to visually read the scene." (Sources: `GDC2008_StylizationWithAPurpose_TF2.pdf`; `NPAR07_IllustrativeRenderingInTeamFortress2.pdf`.)

**Test for:** every visual element can name the gameplay question it answers (e.g. "is this safe to touch?", "friend or foe?"). Flag elements that answer none.

**Anti-pattern:** *decoration-first art* — visuals chosen for prettiness or novelty with no gameplay read attached, producing pretty-but-illegible scenes.

---

## 2. Silhouette is the foundation — validate it as a gate ✅

**Rule:** Design and validate every character, creature, and landmark as a **flat silhouette first**; reject any that aren't distinguishable without internal shading.

**Exemplar:** *Team Fortress 2.* GDC deck: "Character Silhouette: Building block of character design; Identifiable at first read." NPAR §4.1: "the silhouettes of the nine classes were carefully designed to be very distinct… Even when viewed only in silhouette with no internal shading at all, the characters are readily identifiable… used to validate the character design during the concept phase." Valve's *Game Developer* article confirms the workflow: block to silhouette → add detail → convert back to flat black/gray to re-verify. The nine classes get "grossly distinct physical shape" via hats, shoes, and weapons.

**Test for:** render each generated entity as a flat single-color silhouette; confirm a classifier (or distinct-shape heuristic) tells it apart from every other in-tier silhouette above a confidence threshold.

**Anti-pattern:** *silhouette collision* — entities distinguishable only by texture/color, so they become unreadable at distance, in fog, or in shadow.

---

## 3. Value and contrast are eye-direction tools ✅

**Rule:** Grade value so the **highest local contrast falls on the most decision-relevant element**, and keep surrounding areas lower-contrast.

**Exemplar:** *Team Fortress 2.* Two consecutive GDC/NPAR bullets: **"Highest contrast at chest level, where weapon is held"** and **"Gradient from dark feet to light chest."** Value is graded specifically so the eye lands on the held weapon — the element that tells you what threat you face.

**Test for:** in any generated scene/character, the region of maximum luminance contrast coincides with the highest-priority gameplay element (objective, threat indicator, interactive item). If peak contrast lands on background or decoration, flag.

**Anti-pattern:** *contrast noise* — uniformly high-contrast scenes where the eye has no anchor, so critical elements don't pop.

---

## 4. Reserve color for the single most urgent decision ✅

**Rule:** Assign each reserved signal color **exactly one meaning**, tied to the most urgent decision; offload secondary distinctions to silhouette/proportion rather than more colors.

**Exemplar:** *Team Fortress 2.* The "Read Hierarchy" slide: **"Team — Friend or Foe? → Color; Class — Run or Attack? → Distinctive silhouettes / Body proportions / Weapons."** NPAR §4.1: players "must be able to visually identify other players very quickly at a variety of distances and viewpoints in order to assess the possible threat." Color carries the most binary, most urgent decision (friend/foe); shape carries the richer class read.

**Test for:** each signal color maps to exactly one documented meaning; no signal color is reused for a second meaning; the friend/foe (or equivalent urgent) decision is answerable from color alone.

**Anti-pattern:** *cue dilution* — one color carrying several meanings, or the signal color appearing on non-signal decoration, until it means nothing. (Cross-link: the spatial version of this rule is in `open-world-design` §7.)

---

## 5. Photoreal detail fights readability ✅

**Rule:** Cap environmental detail/texture frequency where it would compete with gameplay-signal elements; prefer broad, controlled value/color fields over uniform high-frequency noise.

**Exemplar:** *Team Fortress 2.* NPAR 2007 §4, verbatim: "high frequency geometric and texture detail found in photorealistic games can often overpower the ability of designers to compose game environments and emphasize gameplay features visually." This is *why* TF2 chose stylization — not a limitation of the Source engine, which had already shipped photorealistic HL2/CS:S/DoD:S.

**Test for:** gameplay-signal elements maintain a measurable contrast/frequency margin above their surroundings; if background detail frequency approaches that of signal elements, flag.

**Anti-pattern:** *fidelity creep* — pushing texture/geometry detail everywhere until interactive elements blend into the noise (the root cause of the "yellow paint" crutch in §7).

---

## 6. Pick a named style reference and converge on it ✅

**Rule:** Choose a concrete, **nameable stylistic reference set up front** and make it the convergence target every generated asset is measured against.

**Exemplar:** *Team Fortress 2.* NPAR 2007: "we chose to employ an art style inspired by the early to mid 20th century commercial illustrators **J. C. Leyendecker, Dean Cornwell and Norman Rockwell**." A specific, checkable reference — not "stylized" in the abstract.

**Test for:** the project has a written, specific style bible (named references, palette, line/value conventions) and each generated asset can be scored for conformance.

**Anti-pattern:** *style drift* — "stylized" defined only negatively (not-realistic), so generated assets wander with no shared target.

---

## 7. Signal-color case studies and the cost of the crutch ⚠️

> Everything in this section is **sourced but unverified** (the verification phase was cut short). Use as strong defaults; re-verify before treating as law.

- **Reserved navigational color.** *Mirror's Edge* / *Catalyst* use **red as a single reserved guidance channel** ("Runner Vision" / "Follow the Red") — red marks usable/navigable elements as a diegetic wayfinding aid tied to the protagonist's perception, not an off-screen marker. (Sources: EA "Runner's Vision" article; Gamasutra/Game Developer "Philosophy of Faith" interview.)
- **Reserved interaction color.** *Naughty Dog* games use **yellow** to mark climbable/interactive surfaces and pickups, so players read "go here / use this" without UI. The stated rationale: rising fidelity makes interactive items blend into the environment, so a reserved color keeps them findable. (Source: Game Rant on Naughty Dog color-coding.)
- **The dilution cost (the "yellow paint debate").** Over-applying a signal cue is **immersion-breaking and reads as condescending**, and heavy reliance trains players to ignore other readability channels. The sharpest version: *reliance on explicit paint is itself a symptom of failed environmental art direction* — when silhouette, value, and composition fail to direct the eye, designers fall back on a literal painted marker. (Sources: Wikipedia "Yellow paint debate"; umgamer overview.)

**Test for:** there is one reserved navigation/interaction color with one meaning; it contrasts in every biome (cross-link `open-world-design` §7); and it is *not* doing work that silhouette/value/composition should be doing.

**Anti-pattern:** *paint-as-crutch* — using signal paint to patch a scene that fidelity creep (§5) made illegible, instead of fixing the underlying value/silhouette composition.

---

## 8. Stylization vs. fidelity: the broader trade-off ⚠️

> Sourced but unverified — the BotW claims below did not complete verification.

The often-cited *Breath of the Wild* position (attributed to art director Satoru Takizawa, GDC 2017) is that the painterly style was an **"intentional contraction of reality"** that traded fidelity for readability and tempo, and that the team rejected *Wind Waker*'s heavier stylization because it abstracted too far for intuitive physics communication — i.e. **stylization must still preserve real-world readability cues** so visuals can teach the game's systems. The defensible, engine-agnostic takeaway (which *is* supported by the verified TF2 core): stylization is a tool for readability and longevity (stylized looks age well; photorealism is of its moment), but only when it preserves the cues players reason from.

**Test for:** the chosen style still lets players predict system behavior from appearance (a flammable thing looks flammable; a climbable thing looks climbable).

**Anti-pattern:** *abstraction past legibility* — stylizing so far that players can no longer infer mechanics from looks.

---

## Procedural / AI-authored implication (the crux for Valenfeld)

A headless generator can't "have taste," so readability must be encoded as **machine-checkable constraints** rather than discovered in playtest:

1. **Per-asset read contract.** Every generated asset carries metadata naming the gameplay info it communicates (§1). Assets with an empty read contract are flagged as decoration.
2. **Silhouette-uniqueness gate.** The single most automatable art-readability check (§2): every procedurally-varied creature or legendary item must pass a silhouette-distinctness test against its tier before commit. Route failures back to the generator. (This is the visual sibling of the sameness gate in `procgen-review`.)
3. **Computable value hierarchy.** Peak-contrast-on-signal (§3) is measurable per frame/asset — enforce it as a constraint.
4. **Reserved-color registry.** Signal colors live in a small registry with one-meaning-each (§4); the generator may not emit a reserved color on non-signal assets.
5. **A named style bible as convergence target.** §6 is what gives generated content a coherent house style at all — without a concrete reference set, generated visuals drift. This is the visual analogue of the curated-corpus voice spec in `ai-authored-content-coherence`.

**The unsolved part (honest caveat):** none of the verified sources address how a generator maintains *art-direction coherence across thousands of pieces*. The TF2 corpus proves these are the right per-asset tests; it does not prove they compose into a coherent whole at scale. De-risk early with the oatmeal/silhouette tests in `procgen-review`.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Decoration-first art | Pretty assets with no gameplay read | Per-asset read contract (§1) |
| Silhouette collision | Entities tell apart only by texture/color | Silhouette-first gate (§2) |
| Contrast noise | Uniform high-contrast; eye has no anchor | Peak contrast on the signal element (§3) |
| Cue dilution | One color, many meanings | One reserved color = one meaning (§4) |
| Fidelity creep | Detail everywhere; signals blend in | Cap background frequency (§5) |
| Style drift | "Stylized" = just not-realistic | Named reference convergence target (§6) |
| Paint-as-crutch | Yellow paint patching illegible scenes | Fix value/silhouette, not paint over it (§7) |
| Abstraction past legibility | Can't infer mechanics from looks | Preserve real-world readability cues (§8) |

---

*Sections 1–6 are verification-confirmed against TF2 primary sources; §7–8 are sourced but await re-verification. Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
