# Level Design — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for designing, reviewing, or generating a level. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** choose structural type → build mission graph → plot intensity curve → spatial design (blockmesh) → teaching pass → art. Committing art before validating layout in blockmesh is the most expensive mistake.

---

## A · Guidance language (wayfinding)

**Do**
- [ ] Define the **critical path** as a numbered beat graph before placing any geometry.
- [ ] Establish a **line-of-sight breadcrumb chain** — each crumb visible from the previous one at player eye height.
- [ ] Place ≥1 large, near-constantly-visible **landmark (weenie)** per zone that unambiguously indicates the direction of progress.
- [ ] Light and color-contrast all critical entrances, exits, and interactables with a dedicated **signpost layer** separate from mood/ambient lighting.
- [ ] Maintain an **affordance vocabulary** — novel interactables require a hand-authored antepiece before appearing in any stakes context.

**Don't**
- [ ] Don't leave the critical path visually dark while dead-ends are impressively lit.
- [ ] Don't rely on a single wayfinding channel (light alone, geometry alone) — use ≥2 redundant channels.
- [ ] Don't let procedural geometry accumulate over the weenie sightline.
- [ ] Don't use decoration-looking objects (generic crates, ambient props) as key interactables.

**Test for** — Can a naive player identify the next waypoint within 3 seconds of entering any new space using only environmental cues? From any navigable position, is the zone's landmark visible? Desaturate a screenshot — are critical elements distinguishable by value alone (not hue only)?

---

## B · Structure & gating

**Do**
- [ ] Choose **structural type** (linear / multi-path / hub-and-spoke / open) before layout; it constrains all pacing decisions.
- [ ] Model gating as a **mission graph** (nodes = areas, edges = traversals, conditional edges = locks) with branching factor ≥2 at decision nodes for hub-and-spoke or open structures.
- [ ] Design ≥1 **shortcut** per major zone; announce it from the destination side; verify it reduces traversal to ≤20% of original time.
- [ ] Give every new ability **three uses**: progress past the current gate + shortcut through earlier space + reframe a combat or puzzle.

**Don't**
- [ ] Don't commit to hub-and-spoke without budgeting all spokes at equal quality — players notice the weak spoke immediately.
- [ ] Don't ship a maze floor plan that resolves to branching factor 1 when graphed ("fake complexity").
- [ ] Don't place shortcuts at "cool moment" preference — place them at high-friction traversal points.
- [ ] Don't gate an ability with only use #1 (that's a key, not an upgrade).

**Test for** — Can you draw the level's mission graph in under 60 seconds? Does every decision node have ≥2 branches? After zone completion, is traversal via shortcuts ≤20% of the original time?

---

## C · Teaching through space

**Do**
- [ ] Provide an **antepiece** (consequence-free introduction, no other variables active) for every mechanic before its first lethal encounter.
- [ ] Embed antepieces in **normal-looking level space** — not visually segregated training-ground aesthetics.
- [ ] Cycle each mechanic through **Introduce → Develop → Twist → Conclude** within the zone.
- [ ] Place a **checkpoint before the Twist** so failure doesn't force a replay of Introduce and Develop.
- [ ] Let players encounter each **enemy type in isolation** before mixed-group encounters (the "gym").

**Don't**
- [ ] Don't let any mechanic appear first in a fail state — if first encounter is lethal, the antepiece is missing.
- [ ] Don't place the Twist at the final gate before the exit; it punishes experimentation and blocks mastery.
- [ ] Don't make gym encounters too safe — they must convey the actual danger of the enemy.

**Test for** — Does every mechanic have a consequence-free first encounter? Do all four I→D→T→C phases appear in order in each major zone? Can a player retry the Twist from a checkpoint without replaying Introduce?

---

## D · Encounter & arena design

**Do**
- [ ] Write each arena's **tactical proposition** in one sentence before building any geometry.
- [ ] Provide **both half-height and full-height cover** in every arena; ≥3 cover positions with distinct lines of fire.
- [ ] Place **chokepoints between arenas** (not inside them); defenders should reach them ~5–12 seconds before attackers.
- [ ] Guarantee ≥1 **flanking route** per arena that reaches the enemy's rear without crossing the default forward sightline.
- [ ] Cap combat arenas at **≤3 distinct tactical floor planes**, each with a distinct proposition.

**Don't**
- [ ] Don't build arena geometry before stating its proposition — "looks cool" produces neutral, forgettable space.
- [ ] Don't place cover perpendicular to enemy fire (cover theater).
- [ ] Don't put chokepoints inside arenas (corridor-in-the-middle removes tactical depth).
- [ ] Don't add a fourth floor plane without auditing whether it has distinct tactical value from the existing three.

**Test for** — State the arena's proposition in one sentence. Top-down silhouette: ≥3 cover positions with different fire lines? Count viable chokepoint entry vectors: 1–3 correct; >3 = too open; 0 = griefpoint. Is there a flanking path that avoids the enemy's default forward sightline?

---

## E · In-level pacing

**Do**
- [ ] Plot the **intensity curve** (1–5 per 60-second segment) before building; verify ≥1 valley (≤2) per peak (≥4), final segment ≥4.
- [ ] Insert a **deliberate low-intensity beat** 1–2 sections before the final climax.
- [ ] Give every **rest beat** ≥1 of: loot/reward, narrative information, aesthetic pleasure (view/music), or mechanical preview.
- [ ] Place significant **rewards after** the peak they were earned by — not before it.

**Don't**
- [ ] Don't let intensity creep upward every section — sustained peaks produce fatigue, not excitement.
- [ ] Don't leave rest beats as geometrically empty rooms — they become fast-forwarded on replay and flagged in reviews.
- [ ] Don't place a major reward directly before a hard section; the player enters the boss fight already compensated.

**Test for** — Does the intensity curve end on a peak? Is there ≥1 valley between each consecutive peak pair? For every rest beat, list what the player gains — is the list non-empty?

---

## F · Greybox process

**Do**
- [ ] **Establish player metrics** (bounding box, eye height, jump distance) before placing any geometry.
- [ ] Build every level in **untextured blockmesh** first — expect to rebuild major sections after playtest.
- [ ] Self-playtest ≥3 complete runs; get ≥1 naive tester through before any art pass.
- [ ] **Observe playtests silently** — record deaths, stop points, confusion, positive surprise.
- [ ] Add 20–30% scale to interior spaces to compensate for camera FOV compression.

**Don't**
- [ ] Don't begin an art pass on any section before blockmesh playtest confirms it works.
- [ ] Don't work at 1:1 architectural scale without accounting for FOV — interiors will feel cramped in-engine.
- [ ] Don't ask "what did you think?" — watch behavior; post-play opinions are rationalized and softened.

**Test for** — Has the level been self-playtested ≥3 full runs and naively playtested ≥1 time before art begins? Can you list the top 3 failure points by observed player behavior — not designer intuition?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Mission graph** with structural type and branching-factor constraints → **graph validator** (reject levels with branching factor <2 at all decision nodes; reject hub-and-spoke structures missing ≥1 shortcut per major traversal).
- [ ] **Intensity curve** as a numeric sequence with valley/peak constraints → **curve linter** (reject curves with no valley between peaks, or that don't end on a peak).
- [ ] **Mechanic sequencing table** (which mechanics appear, in which order) → **antepiece sequencing validator** (reject any level where a mechanic's first appearance is in a lethal-stakes slot rather than a consequence-free slot).
- [ ] **Affordance vocabulary** of approved interactable types → **vocabulary validator** (reject novel interactables without a hand-authored antepiece slot assigned upstream).
- [ ] **Sightline validation pass** at player eye height → **sightline validator** (ray-cast from each beat entrance to the next; ray-cast from ≥60% of navigable floor area to the weenie; 3D only — cannot infer from 2D graphs).
- [ ] **Arena proposition tag** required before geometry instantiation → **proposition validator** (reject arenas without a one-sentence proposition tag; flag for human review rather than auto-fail).
- [ ] **Player metrics contract** (bounding box, hallway widths, door dimensions, jump clearances) → **metrics validator** (reject geometry that clips the player capsule or fails minimum clearances).

> Gate every generated content batch through `procgen-review` for a final holistic pass before committing.
