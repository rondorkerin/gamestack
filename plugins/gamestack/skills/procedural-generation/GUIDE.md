# Procedural Generation — Guide

How to build generators whose output is perceptually rich, coherent, and meaningful. Pair with `CHECKLIST.md`, and gate every generator's output with `procgen-review`.

> Sources: Kate Compton ("10,000 Bowls of Oatmeal"), Jason Grinblat & Brian Bucklew (*Caves of Qud*, FDG'17 *Subverting Historical Cause & Effect*), Derek Yu (*Spelunky*), Tarn Adams (*Dwarf Fortress*), Emily Short, plus the open-world procgen discourse (Minecraft, Valheim, No Man's Sky, Daggerfall, TotK).

---

## 1. The 10,000 Bowls of Oatmeal problem

Kate Compton's framing is the spine of this whole skill:

> *"I can easily generate 10,000 bowls of plain oatmeal, with each oat being in a different position… mathematically speaking they will all be completely unique. But the user will likely just see a lot of oatmeal. Perceptual uniqueness is the real metric, and it's darn tough."*

No Man's Sky (early) and Daggerfall are the cautionary tales: vast, technically-infinite, perceptually repetitive. Daggerfall generated ~188,000 sq mi, 15,000 towns/dungeons, 750,000 NPCs (Guinness) — and was "not particularly interesting." "Bigger isn't always better." Even *Elden Ring* drew the critique for copy-pasted catacombs.

**The metric:** would a player who has seen 10 instances find the 11th worth visiting? If not, you have perceptual sameness, no matter how unique the math.

---

## 2. Define constraints first — what makes a *good* artifact and a *bad* one

Before building any generator, enumerate:
- The concrete properties of a **good** artifact ("the easiest generators to make are the ones where you can describe good artifacts as sets of concrete properties").
- The **hard constraints** — things that must *never* happen. Star these; they're inviolable.

A generator without a definition of "good" can only produce noise that's occasionally acceptable. With one, you can filter, weight, and reject.

---

## 3. The hybrid model: handcrafted anchors + constrained fill

The consistent lesson across Minecraft, Valheim, Terraria, Remnant, Spelunky, Diablo, and Caves of Qud: **never pure generation.** Pair handcrafted elements with procedural fill, and give each biome its own rules.

- **Hand-author the static backbone** — Caves of Qud kept "a static backbone to deliver some of the deep worldbuilding and lore… the procedural elements served the other goal." For an open-world RPG: finite legendary loot, named landmarks, the mythic spine, key quests.
- **Proceduralize the connective tissue** — the dungeons, wilderness, minor NPCs, ambient books, most history.
- **Spelunky's spatial version** — a guaranteed solution path is carved through a 4×4 grid; each room is drawn from handcrafted **templates** with probabilistic trap/loot placement. Handcrafted chunks, procedural assembly.
- **Touch-up passes** — Todd Howard on procgen for Elder Scrolls: "where it's not working out, we go in and touch it." Generate a draft, then add hand-crafted fine touches (or, for an AI engine, a review-and-repair pass — see `procgen-review`).

**Threshold:** if output keeps failing the oatmeal/fanfic tests after corpus + constraint tuning, **shift the ratio toward more hand-authored anchors.**

---

## 4. Generate from a voice-consistent corpus

The key to a single identity across generated text. *Caves of Qud* "relies heavily on its corpus of text — over 40,000 words" so generated books, item descriptions, and place names inherit the same diction as the handwritten text. Generated content then "feels like it belongs in the same place" rather than sticking out.

For an LLM-authored engine this is the equivalent of a tight style/voice spec plus exemplars: every generation call writes *in the established voice*. See `ai-authored-content` for the full treatment.

---

## 5. Constrain references to an established context pool

Caves of Qud generates history period-by-period; later events may reference only regions and figures **already generated**, keeping causality internally consistent. Generalize: a generator should only reference canon that already exists in its context pool, never inventing contradictions.

A complementary trick — **generate the event, rationalize after** (Qud's ex-post-facto causation): produce a historical event, then have the system explain it in terms of existing world facts. Assign generated figures recurring thematic **domains** so their deeds cohere into a recognizable arc rather than random noise.

---

## 6. Intentionality and local logic — the dividing line

Handcrafted worlds work because "every area is logically related to surrounding areas." Procedural content approaches this only when each element is tied to **local context and meaning**:

- A ruined cabin implies former inhabitants (RDR2-style environmental storytelling).
- A dungeon reflects its region's lore and contains **region-appropriate rewards** (Elden Ring: catacombs hold Grave Glovewort for summons; mines hold Smithing Stones for weapons; Hero's Graves hold chariot challenges).
- Each location has a **clean completion arc** (Elden Ring mini-dungeons end with a Site of Grace and a boss).

**Generate reasons, not just geometry:** *who built this, what happened here, why is this reward here.* This is the single highest-leverage rule for making generated content feel authored.

**Anti-pattern:** geometry with no backstory; rewards disconnected from context; locations that ignore their neighbors; the "icon janitor" metagame where clearing order carries no in-world meaning.

---

## 7. Make systems multiplicative, not additive

The antidote to templated output. Rather than authoring every interaction, define a small set of properties/rules that **combine**:

- BotW's chemistry engine — fire spreads, metal conducts lightning, wind carries flame. Dohta (2017): "a world where combining simple elements could produce complex results."
- TotK's "multiplicative gameplay" — the enabler (Takayama, GDC 2024) was making *everything* physics-driven: "removing non-physics-driven objects and making everything physics-driven" so interactions emerge "without any dedicated implementation."

A telling success signal: **the developers kept discovering unintended solutions to their own puzzles.** For a generator, this means authoring affordances and rules, not solutions — emergence does the combinatorial work that templates can't. (Deeper in `systemic-emergent-design`.)

**Anti-pattern:** single-solution "additive" content where each puzzle has one scripted answer; systems that don't interact.

---

## 8. Curate randomness

Raw `random()` produces clumps and repeats. Shape it:
- **Weighted distributions** for frequency control.
- **Deck-shuffle / draw-without-replacement** to avoid back-to-back repeats.
- **Barnacling** — place medium objects around large ones, small around medium, for natural composition.

---

## 9. Tie generation to mechanics, and lean on apophenia

- **Mechanical correlation** (Emily Short's rule): purely decorative procgen (funny names, random constellations) is seen through quickly; content that *correlates with gameplay* stays interesting. Generate things that matter mechanically.
- **Apophenia by design**: generate suggestive, resonant juxtapositions and trust the player's pattern-seeking mind to author meaning. Dwarf Fortress's emergent sagas (the "Bronze Murder," "Boatmurdered") are the proof — a coherent-enough simulation lets players write the epics.

---

## 10. The two tests you run constantly

These belong to `procgen-review` but drive design too:

- **The oatmeal test** — sample outputs blind; can a tester (or a reviewing agent) tell them apart? If not → perceptual sameness.
- **The fanfic/retell test** — would a player care enough to *retell* this item/quest/event? Dwarf Fortress sagas pass; an algorithmically-unique-but-bland item fails. If it fails → increase systemic depth or hand-authored anchoring.

**For an AI authoring engine specifically:** build an **explicit self-review pass** that compares each new instance against prior instances and flags structural or thematic duplication — the repetition a human designer would catch but a single-sample generator can't. That pass is `procgen-review`.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Oatmeal | Infinite but perceptually identical | Constraints + anchors + corpus depth |
| Wide-but-shallow | Scale as a marketing number | Depth/meaning over raw size |
| No anchors | Pure unconstrained generation | Handcrafted backbone + constrained fill |
| Geometry without reason | Locations with no who/what/why | Local logic; region-appropriate rewards |
| Decorative procgen | Random names/cosmetics only | Tie generation to mechanics |
| Additive content | One scripted solution each | Multiplicative, interacting systems |
| Voice drift | Generated text doesn't match | Voice-consistent corpus / style spec |
| Canon contradiction | History references nonexistent facts | Constrain refs to established context pool |

---

*Gate everything this skill helps you build with `procgen-review`. Expand with your own generator post-mortems and citations — see CONTRIBUTING.md.*
