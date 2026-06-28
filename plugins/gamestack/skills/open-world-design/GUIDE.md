# Open-World Design — Guide

The spatial discipline of open worlds: how terrain, landmarks, navigation, and pacing make a world that players *want* to move through. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from the design discourse around *Breath of the Wild* / *Tears of the Kingdom*, *Elden Ring* / Souls, *Morrowind*, *Ghost of Tsushima*, *Subnautica*, and the critique of the "Ubisoft formula." Sources noted inline.

> **The central law:** make every patch of the map either *visibly promising* or *quietly hiding something worth finding*. Pull players through the world; don't push them down a dotted line.

---

## 1. Terrain is a communication system, not scenery

**The triangle rule** (Nintendo, CEDEC/GDC 2017 "Breaking Conventions with BotW", Dohta & Fujibayashi; surfaced in English by Capcom's Matt Walker). Build the landscape mostly from triangular forms at **three deliberately-distinct scales**:

- **Large** → landmarks. Visible across the region; the player's eye is drawn to the *tip*, so place a point of interest at the peak (Mark Brown, GMT).
- **Medium** → occluders. They block the view of what's behind them, creating surprise ("what's over there?") and the **climb-over-or-go-around** choice.
- **Small** → texture and tempo.

Keep the three scales clearly distinct — a "muddy middle" of shapes that are neither clearly landmark nor clearly texture reads as noise. (Subtle layer: BotW's *irregular* triangles, with extra peaks or divots, tended to hide Koroks — drawing the eye without the player knowing why.)

**Why it matters for a generator:** these are directly portable rules. An AI authoring terrain can be constrained to produce the three scales, place POIs at peaks, and guarantee occlusion.

**Anti-pattern:** flat, uniform, or fully-visible terrain with no occlusion (Daggerfall's "open desert"); a muddy mix of indistinct shapes.

---

## 2. Gravity and curved paths disperse and pull player flow

Nintendo's playtest heatmaps showed players clustering on a few discrete paths and getting "lost in a bad way" when they diverged. Their fixes:

- **Gravity / funnels** — bowls and valleys that make players *orbit* landmarks. Lower main paths (canyons, river valleys) below the surrounding terrain so players "roll into them like a boulder into a pit."
- **Curve the critical path** — never a straight line. The next landmark should reveal only as the player rounds the bend, so discovery is continuous.
- **Lure under-visited areas** — when heatmaps showed dead zones, the team *added* attractions there. The procedural equivalent: detect low-traffic regions and place new POIs to pull players in.

**Anti-pattern:** a single golden path everyone funnels down; seeing a region's entire contents at once, killing the reveal.

---

## 3. Interconnect the world; make shortcuts real

Dark Souls' Lordran is the benchmark: every area connects logically into a larger whole, with shortcuts (elevators, ladders, opened gates) that are **real traversable geometry** looping back to hubs like Firelink Shrine — "a path that could be reached in a straight line is forcibly made into a circle." Loops counterbalance the sting of death and create "aha" moments (open a gate, find yourself near where you began). Bloodborne stacks Yharnam vertically; Miyazaki confirms areas stay physically seamless even where hubs offer warps.

**Test:** does unlocking a shortcut open a genuine path that shortens future travel? Do layouts loop back rather than dead-end?

**Anti-pattern:** disconnected "levels" joined only by loading tunnels or fast travel; shortcuts that are visual tricks, not connections.

---

## 4. Give every biome an unmistakable identity

*Elden Ring* makes every region readable at a glance: Limgrave (golden fields), Caelid (scarlet-rot wasteland), Liurnia (lakes + glowing academy), Siofra River (alien glow underground). "You know where you are just by looking around." *Subnautica* does it underwater — each biome recognizable by its landmarks, lifeforms, and flora, growing more alien as you descend.

Each biome needs a **unique dominant color, signature flora/architecture, and silhouette**, with transitions between them (a snow biome abutting a desert with no gradient is a generation fault).

**Test:** could a player name their region from a single screenshot with no HUD?

**Anti-pattern:** interchangeable regions ("once you've seen a biome, you've seen most of it" — the Valheim critique).

---

## 5. Verticality is a full design axis

*Tears of the Kingdom* (Fujibayashi, "Ask the Developer Vol. 9"): the previous game's height was "largely two-dimensional"; TotK built "a three-dimensional world… travel from the surface to the skies seamlessly." Each layer carried a **distinct emotional purpose** — bright, exhilarating sky islands vs. the dark, dangerous Depths (Aonuma: contrasting them "was an intentional choice… to make the gameplay more varied").

Crucially the Depths was **production-efficient *and* navigationally legible**: it mirrors the surface map exactly, with a "Lightroot" beneath every surface Shrine (named with the Shrine's name reversed). A known layer becomes the key to an unknown one — a powerful, generator-friendly trick.

**Test:** does the Z-axis carry meaningful gameplay (not just climbable walls)? Do layers contrast emotionally? Can a known layer key an unknown one?

**Anti-pattern:** decorative "height"; identical layers; an underground with no relationship to the surface above.

---

## 6. Navigation: prefer diegetic cues over HUD markers

The strongest worlds guide through the world itself:

- *Ghost of Tsushima* — the **Guiding Wind** bends grass toward your destination; birds and foxes lead to collectibles. Framed in-fiction ("your father is a wind at your back").
- *Morrowind* — NPC directions force players to build a real mental map; over time "the journeys become less about following directions and more about following firsthand knowledge of the land."
- *Elden Ring* — the **Guidance of Grace** golden trail *suggests* a direction without a hard waypoint.

The cost of hard markers: "the world around them becomes an obstacle in the path of their destination." A floating arrow makes players watch the minimap instead of the world.

**Friction is a designed choice, not a default.** Fast travel and markers reduce friction *and* immersion. Make fast travel **diegetic** (Morrowind's silt striders existed in the world and doubled as info sources; a token economy can gate it). Dark Souls withheld fast travel for the first half specifically to teach the world's interconnection. But calibrate to audience — ESO's Matt Firor: a no-map, "third tree on the right" game would find "very few" players today. Each friction removed is immersion lost; remove it on purpose.

**Anti-pattern:** GPS markers that let players skip all engagement with the environment; *or* friction so high it becomes tedious (vague directions that send players in circles).

---

## 7. Reserve a signal color for interactables

BotW reserved a specific **orange** for unsolved shrines, puzzle orbs, and unopened chests — and avoided that orange elsewhere — so important things reliably "pop." The governing principle (Nintendo): *"using a cue incorrectly dilutes its meaning."*

Cautionary contrast: TotK's shrine swirl blended into the landscape's greens/blues and got harder to find. Horizon's yellow handholds were criticized as hard to see in bad weather (Guerrilla added a Focus-pulse reveal). So: one reserved color, used *only* for interactables, with **enough contrast against every biome's palette** — or an on-demand reveal pulse.

**Test:** is there one color reserved exclusively for interactive objects, and does it contrast in *every* region?

**Anti-pattern:** signal colors that blend into the environment; overusing the signal color until it means nothing.

---

## 8. Exploration pull and reward cadence

Make movement pay off, at two scales:

- **Micro-rewards** reinforce curiosity. BotW's Korok Seeds are "Where's Waldo moments" rewarding the smallest inkling of curiosity (lift a rock, dive through a ring). Note the restraint: of 900, only **441** are needed to max inventory — the surplus exists purely to reward noticing, and the full set yields only an ornamental gift. The *finding* is the reward.
- **Macro-rewards** provide the long arc: finite, meaningful loot (see `rpg-systems`).

**The intrinsic-motivation test** (Chris McDonald on BotW): *would the player still do this if the extrinsic reward were removed?* "Would I climb the castle for the view? Definitely. Would I mine rocks if they weren't sellable? No way." Aim for "yes" on exploration content; extrinsic rewards should *augment* curiosity, not replace it.

**The pull** (Todd Howard's "see that mountain, you can climb it"): from any standing position the player should see at least one intriguing thing inviting movement toward it — created by medium occluders, distant towers, oddly-placed objects.

**Anti-pattern:** activities that exist only to deliver a reward (collect-100-flags), the "hamster wheel" / "icon janitor" checklist; empty vistas with nothing to pull toward.

*(For the general theory of reward schedules and intrinsic vs. extrinsic motivation, see `game-design-fundamentals`.)*

---

## 9. Spatial pacing: the wonder→fear gradient and tension/release

- **Engineer an emotional gradient.** *Subnautica* is the masterclass — wonder and fear rise with depth, each biome more alien and threatening, no map to reassure ("there's really nothing more alien on Earth than the ocean"). TotK's Depths apply the same fear-engineering as a counterweight to the sky's exhilaration (Fujibayashi compared it to a haunted house). **Caution:** removing orientation entirely can make boundaries feel *unfair* — pair the fear gradient with enough orientation that danger feels earned.
- **Tension and release.** Souls uses shortcuts back to safe hubs (Sites of Grace restore HP, refill flasks, respawn enemies) to vent tension after dangerous stretches. RDR2 deliberately inserts quiet, slow moments before high-intensity beats. Alternate intensity with recovery; don't run wall-to-wall combat *or* so much downtime the world feels empty.

**Test:** does emotional tone vary across the world (safe→dangerous, familiar→alien)? After a high-tension stretch, is there a release valve?

---

## 10. Visual orientation: landmarks, light, silhouette

A constant mega-landmark anchors the whole map. *Elden Ring*'s **Erdtree** dominates the view from nearly everywhere — players "gravitate toward the big bright glowing tree" consciously or not, so it signals importance *and* orients. FromSoftware deliberately oversizes land formations to "dictate exploration and provide a constant sense of grandeur." Alien silhouettes (BotW's Sheikah architecture) draw the eye by standing out from natural forms.

Prioritize **readability over fidelity** (Team Fortress 2 abandoned realism because it "made it impossible to read the battlefield"; BotW's painterly style was chosen to guide attention — "an intentional contraction of reality"). Readability first, personality second, fidelity never. *(Full treatment in `art-direction-and-readability`.)*

**Test:** is there a constant visible mega-landmark for orientation? Do important objects have distinct silhouettes? Does lighting direct the eye to focal points?

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Icon vomit / icon janitor | Map blanketed in markers; clearing order is meaningless | Diegetic discovery; reserved signal color; tie content to local meaning |
| Marker tunnel vision | Player watches minimap, not world | Soft/diegetic guidance; landmarks and sightlines |
| Wide-but-shallow | Vast map, repetitive content (Daggerfall, NMS) | Density of *meaningful* content over scale; hand-authored anchors |
| Muddy terrain | Shapes neither landmark nor texture | Three distinct triangle scales |
| Samey biomes | Regions blur together | Unique palette/flora/silhouette + transitions |
| Dead travel | Empty stretches between POIs | Tune distance-between-interesting-things; micro-reward cadence |
| Decorative verticality | Height with no gameplay | Z-axis as a real axis; emotional layer contrast |
| Frictionless fast travel | Teleport anywhere from the start | Diegetic, gated fast travel; earn shortcuts |
| Unfair disorientation | No orientation, boundaries feel arbitrary | Pair fear gradient with landmarks/keys |

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
