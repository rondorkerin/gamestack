# Open-World Design — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for the spatial side of an open world. Run these against a map, a region, or generated terrain. See `GUIDE.md` for the reasoning and sources.

---

## Terrain & layout

**Do**
- [ ] Build terrain from triangles at **three distinct scales**: large (landmarks), medium (occluders), small (texture).
- [ ] Place a point of interest at the **tip** of large triangles — the eye goes there.
- [ ] Use medium occluders so the player **can't see everything at once**; create the climb-over-or-around choice.

**Don't**
- [ ] Don't ship flat, uniform, or fully-visible terrain (no occlusion = no surprise).
- [ ] Don't leave a "muddy middle" of shapes that are neither clearly landmark nor texture.

**Test for** — Does every region have ≥1 large landmark visible from afar? Are the three scales clearly distinct?

---

## Flow & gravity

**Do**
- [ ] Lower main paths (valleys, canyons) below surrounding terrain so players **flow into them**.
- [ ] **Curve** the critical path so the next landmark reveals as the player rounds a bend.
- [ ] **Lure under-visited areas** — add attractions where traffic is low.

**Don't**
- [ ] Don't create a single golden path everyone funnels down.
- [ ] Don't reveal a region's entire contents at once.

**Test for** — Are there multiple viable routes between any two POIs? Do roads/valleys sit lower as natural channels?

---

## Interconnection

**Do**
- [ ] Make shortcuts **real traversable geometry** that loops back to hubs and shortens future travel.
- [ ] Loop layouts back on themselves rather than dead-ending.

**Don't**
- [ ] Don't join "levels" only by loading tunnels or fast travel.
- [ ] Don't fake shortcuts as visual tricks.

**Test for** — Does unlocking a shortcut open a genuine path? Do region layouts loop?

---

## Biome identity

**Do**
- [ ] Give each biome a unique **dominant color, signature flora/architecture, and silhouette**.
- [ ] Put **transitions** between contrasting biomes.

**Don't**
- [ ] Don't make regions interchangeable.
- [ ] Don't abut wildly different biomes with no gradient.

**Test for** — Could a player identify their region from one screenshot with no HUD?

---

## Verticality

**Do**
- [ ] Use the Z-axis for **meaningful gameplay**, with layers that **contrast emotionally** (safe/dangerous, bright/dark).
- [ ] Let a known layer act as a **navigation key** to an unknown one (TotK Depths ↔ surface).

**Don't**
- [ ] Don't add purely decorative height.
- [ ] Don't build an underground with no relationship to the surface.

**Test for** — Does vertical space carry gameplay, not just climbing? Do layers contrast?

---

## Navigation & friction

**Do**
- [ ] Let players reach objectives via **in-world cues** (landmarks, NPC directions, environmental signals).
- [ ] Keep any HUD guidance **optional and non-intrusive** (a soft golden-trail at most).
- [ ] Make fast travel **diegetic** and gated (in-world transport, token cost).
- [ ] Treat every friction removal as a **deliberate tradeoff**, calibrated to audience.

**Don't**
- [ ] Don't use a floating arrow / dotted line that makes players watch the minimap.
- [ ] Don't enable instant fast-travel-from-anywhere that hollows the world.
- [ ] Don't make directions so vague they send players in circles.

**Test for** — Can the player navigate by the world alone? Does fast travel have an in-world justification?

---

## Signal color

**Do**
- [ ] Reserve **one color exclusively** for interactive/important objects.
- [ ] Ensure it **contrasts in every biome** (or add an on-demand reveal pulse).

**Don't**
- [ ] Don't let the signal color blend into any biome (the TotK-shrine mistake).
- [ ] Don't overuse it elsewhere — that dilutes its meaning.

**Test for** — Is there one reserved interactable color, and does it pop in every region?

---

## Exploration pull & reward cadence

**Do**
- [ ] From any standing position, show **≥1 intriguing thing** that invites movement toward it.
- [ ] Maintain a steady cadence of **micro-discoveries** (roughly every few minutes of exploration).
- [ ] Keep macro-rewards **finite and meaningful**.

**Don't**
- [ ] Don't build activities that exist only to deliver a reward (collect-100-X).
- [ ] Don't create empty vistas with nothing to pull toward.

**Test for (the key one)** — For each exploration activity: **would players still do it if the reward were removed?** Aim for yes.

---

## Spatial pacing

**Do**
- [ ] Vary emotional tone across the world (safe→dangerous, familiar→alien); build a **wonder→fear gradient**.
- [ ] After high-tension stretches, provide a **release valve** (safe hub, shortcut, downtime).
- [ ] Pair fear/disorientation with **enough orientation** that danger feels earned.

**Don't**
- [ ] Don't keep a uniform emotional tone everywhere.
- [ ] Don't run wall-to-wall combat, or so much downtime the world feels empty.
- [ ] Don't disorient so totally that boundaries read as unfair.

**Test for** — Does tone vary across regions? Is there tension/release rhythm?

---

## Visual orientation

**Do**
- [ ] Provide a constant, visible **mega-landmark** (Erdtree-equivalent) for orientation.
- [ ] Give important objects **distinct silhouettes**; use lighting to mark focal points.
- [ ] Prioritize **readability over fidelity**.

**Don't**
- [ ] Don't use uniform lighting with no focal hierarchy.
- [ ] Don't let important objects share silhouettes with background clutter.

**Test for** — Is there a constant mega-landmark? Are key objects readable by silhouette alone?
