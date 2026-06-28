# Open-World Design — Guide

The reasoning behind good open worlds. Pair this with `CHECKLIST.md` for the actionable version.

> **v0.1 baseline.** This captures widely-accepted open-world design principles (drawn from the design discourse around games like *Breath of the Wild*, *Elden Ring*, *Red Dead Redemption 2*, *The Witcher 3*, and the critique of the "Ubisoft formula"). It's a foundation to be deepened with cited research and case studies.

---

## 1. The central tension: space vs. density

Open-world design is a constant negotiation between two things players want that pull against each other:

- **Scale** — the awe of a vast, continuous, seamless world.
- **Density** — the feeling that wherever you go, something interesting happens.

You cannot maximize both with finite resources. A 100 km² map with the content budget of a 20 km² map will feel hollow. The classic failure is **scale chasing**: marketing a map size, then padding it with copy-pasted activities to fill the void.

**Design rule of thumb:** decide your *target time-to-interesting* — how long a player should be able to travel in any direction before something grabs their attention (a landmark, an encounter, a discovery). Then size the world to your content budget, not the other way around. *Breath of the Wild* famously tuned this with the "triangle rule" — you should almost always see a point of interest, and something is always pulling your eye.

---

## 2. The explore → reward loop (the compulsion loop)

Exploration sustains itself only if curiosity is reliably rewarded. The loop:

1. **Notice** — something pulls the eye (a tower, ruin, light, smoke, oddity on the horizon).
2. **Travel** — getting there is itself interesting (traversal, micro-encounters).
3. **Discover** — arriving reveals something: a reward, a view, a story beat, a mechanic.
4. **Reward** — the payoff justifies the detour and *re-primes* curiosity for the next thing.

The loop breaks when:
- The reward is **predictable and identical** every time (every tower = same map reveal). Curiosity dies once the player can predict the payoff.
- The reward is **purely numeric** (loot with no meaning, +5 of a stat you don't track).
- **Travel is dead time** — empty terrain with nothing en route.

**Vary the reward type.** Mix: tangible loot, knowledge/lore, a new shortcut or traversal option, a mechanical unlock, an authored vista or "wow" moment, a character/quest hook. Unpredictability of *which kind* of reward is what keeps the loop alive.

---

## 3. Navigation, landmarking & wayfinding

Players need to build a mental map. They do this through **landmarks**, not minimaps.

- **Lead the eye with landmarks.** Tall, distinctive silhouettes (a mountain, a tower, a glowing tree) act as beacons that orient the player and create intention ("I'll head toward that"). This is *pull-based* exploration — the world invites you rather than an arrow pushing you.
- **Composition matters.** Frame views so that from any high point, the player sees 2–4 enticing destinations. Use the landscape's lines (rivers, ridges, roads) to funnel the gaze toward them.
- **Layer your guidance.** Diegetic cues (a road, a trail of crows, distant smoke) feel like discovery; UI markers (waypoints, minimaps) feel like chores. Lean diegetic. *Elden Ring*'s minimal guidance and *Ghost of Tsushima*'s guiding wind are the modern reference points for reducing UI dependence.
- **Sense of place.** Each region should be instantly recognizable by silhouette, palette, biome, and architecture. If the player can't tell *where* they are from a screenshot, regions are too samey.

---

## 4. Points of interest (POI): density, variety, pacing

POIs are the beats of the world. Get three things right:

- **Density / spacing** — tune to your time-to-interesting. Too sparse = empty; too dense = the world becomes a checklist and loses breathing room. Build in deliberate quiet stretches so peaks land harder.
- **Variety** — rotate POI *types* (combat encounter, puzzle, environmental story, vista, NPC/quest hook, resource, secret). A world of one repeated POI type is the "copy-paste" failure.
- **Layered legibility** — a POI should read at three distances: a silhouette that pulls you from afar, a "what is this?" hook on approach, and a payoff on arrival.

**The map-marker trap ("icon vomit"):** dumping every POI onto the map as an icon converts exploration into list-clearing. Players stop *looking at the world* and start *driving to dots*. Mitigations: reveal POIs through play rather than pre-marking them; let the player discover by looking; keep the map clean; make the *world* legible enough that you don't need the map.

---

## 5. Traversal & movement (the verb you do most)

Players spend more time moving than doing almost anything else, so **traversal must be intrinsically enjoyable** — it's the connective tissue of the whole experience.

- **Game feel first.** Movement should be responsive and satisfying on its own (the joy of *BotW* climbing/gliding, *Spider-Man* swinging, *RDR2* horse-riding momentum).
- **Traversal as a system, not a skip.** Fast travel relieves tedium but, overused, hollows out the world you built. Gate or cost it so the world stays the primary stage; *earn* shortcuts through exploration (unlockable mounts, grappling points, discovered routes).
- **Verticality** multiplies a world's content per square meter and creates natural landmarks and sightlines. Climbing, gliding, and layered terrain make a smaller map feel larger and richer.
- **Friction is a tool.** Some games (*RDR2*, *Death Stranding*) make traversal deliberately effortful to create atmosphere and weight. Choose your friction intentionally — it's a design lever, not a bug.

---

## 6. Systemic & emergent design

The most replayable open worlds are **systems, not scripts**. Authored content is finite and consumed once; systemic interactions generate fresh situations forever.

- **Consistent, interacting rules.** *Breath of the Wild*'s "chemistry engine" (fire spreads, metal conducts, wind carries) means players invent solutions the designers never scripted. Define a small set of rules that combine, and let players experiment.
- **Reactive world.** NPCs with schedules, dynamic weather, wildlife, faction behavior — these make the world feel like it exists without the player. *RDR2* sets the bar for reactivity and incidental detail.
- **Emergence > enumeration.** A handful of systems that multiply against each other beats a long list of one-off scripted events. Author the *rules and the spaces*; let players author the *stories*.

---

## 7. Quest & content structure in open space

- **Anchor freedom with spine.** Pure freedom can leave players aimless; a strong critical path (a "spine") gives direction while side content provides the freedom. *The Witcher 3* is the reference for side quests that are authored, characterful, and worth doing — the opposite of filler.
- **Avoid "content as chores."** Generic radiant/filler quests (clear N camps, collect N feathers) pad playtime but erode goodwill. If a quest exists only to inflate hours, cut it.
- **Respect player-authored goals.** The best open worlds let the player set their own objective and support it, rather than forcing one ordering.

---

## 8. Common failure modes (the anti-patterns)

| Anti-pattern | What it looks like | Why it fails |
|---|---|---|
| **Scale chasing** | Marketing map size; padding to fill it | Density collapses; world feels empty |
| **Icon vomit** | Map blanketed in markers | Exploration becomes list-clearing |
| **Copy-paste content** | The same camp/tower/outpost everywhere | Curiosity dies once the reward is predictable |
| **Dead travel** | Long empty stretches between POIs | Traversal becomes a loading screen |
| **Identical rewards** | Every discovery gives the same thing | Loop stops re-priming curiosity |
| **UI-led exploration** | Follow-the-arrow with no diegetic cues | Player watches the minimap, not the world |
| **Fast-travel everywhere** | Instant teleport from the start | Hollows out the world you built |
| **Samey regions** | Biomes blur together | No sense of place; no mental map |
| **Bloat** | Hundreds of hours of filler | Padding ≠ value; erodes goodwill |

---

## 9. A practical design sequence

When designing a region from scratch, a workable order:

1. **Fantasy & pillars** — what's the core experience and the 3–4 design pillars? Every decision serves these.
2. **Content budget** — honestly estimate how much real content you can build.
3. **Size to the budget** — derive map size from target density, not ambition.
4. **Landmark composition** — place the big silhouettes that orient and pull.
5. **POI layout & pacing** — distribute varied POIs; build in quiet stretches.
6. **Traversal & systems** — make moving fun; define the interacting rules.
7. **Critical path** — lay the spine through the space.
8. **Playtest the loop** — drop a fresh player in and watch: do they explore unprompted? Is curiosity rewarded? Where's the dead time?

---

*Expand this guide with cited research, GDC talk references, and named case studies. See CONTRIBUTING.md.*
