# Systemic-Emergent Design — Guide

How to make play *emerge* from a small set of consistent, interacting rules instead of being scripted moment by moment — and why that substrate is the precondition for procedural content that coheres. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from the immersive-sim lineage (Looking Glass → Ion Storm → Arkane; Warren Spector, Doug Church, Harvey Smith, Raphael Colantonio), Jesper Juul's emergence/progression distinction, Nintendo's *Breath of the Wild* / *Tears of the Kingdom* "chemistry engine" talks, Tynan Sylvester's *RimWorld*, and Kate Compton's "oatmeal" critique of procedural generation. Sources noted inline.

> **The central law:** *author affordances, not solutions.* You design the rules, never the play (the second-order design problem). Keep rules near-exceptionless and real-world-mapped so they're learnable by exposure; guarantee intention + perceivable consequence for every verb; and freeze the rule substrate before generating content over it.

> For perceptual uniqueness and the handcrafted-anchor + constrained-fill hybrid, see `procedural-generation` — this skill is its systemic counterpart: the rules that make generated content *mean* something.

---

# Sub-domain 1 — The immersive-sim lineage & the definition

> The immersive sim (*Ultima Underworld* 1992 → *System Shock* → *Thief* → *Deus Ex* → *Dishonored* / *Prey*) uses simulated systems that respond to a broad array of player actions, enabling solutions "beyond what has been explicitly designed." Spector is credited with the term "immersive sim" (Deus Ex post-mortem, 2000) but attributes it to Doug Church. The recurring metaphor: a tabletop game run by a good gamemaster — rules keep it a game; the game reacts to the player.

## 1.1 State world rules as universal, not per-object, behaviors

Define what *classes* of things do (flammable, conductive, climbable), not what individual scripted objects do. Arkane is "built on a set of principles related to consistent, coherent game systems … to create spaces that enable the player to improvise" (Colantonio & Smith, GDC 2013). *Prey*'s GLOO Cannon was an enemy-incapacitation tool, but because it obeyed a general "creates a climbable surface" rule it became a universal traversal/puzzle device — its own systems designer was surprised by what players did with it.

- **Test for:** every interactive property resolves to a named world-rule class; zero properties are object-unique scripts. Any object whose behavior can't be expressed as `{material tags} × {global rules}` is a violation.
- **Failure mode — The Fabergé Object:** a one-off scripted interaction that works only on a single hand-placed object and breaks the player's learned model of the world.

## 1.2 Guarantee Intention and Perceivable Consequence for every core verb

Doug Church ("Formal Abstract Design Tools," 1999): *Intention* = "making an implementable plan of one's own creation in response to the … game world"; *Perceivable Consequence* = "a clear reaction from the game world to the action of the player." Both derive from games where every action yields direct, visible feedback (Mario 64).

- **Test for:** for each verb the design specifies (a) what plan it lets the player form and (b) the immediate, perceivable world reaction. A consequence the player can't perceive within one action-feedback loop fails.
- **Failure mode — The Invisible Lever:** the world reacts, but the player can't perceive it, so they can't learn the rule or form intentions around it.

**→ Procedural / headless implication.** The generator must treat the world-rule set as a *read-only contract*: generated content may compose rules but may never introduce a new exception. **Hand-author** the rule substrate (the "chemistry table") and the canonical tutorialization of each rule; generation is safe only for *composing* already-consistent rules.

---

# Sub-domain 2 — Emergence from few deep systems vs. scripted events

> Jesper Juul (*Half-Real*, 2005) splits games into *emergence* (simple rules combining into interesting variation) and *progression* (serial, scripted challenges). His operational test: **"if the gamefaq is a walkthrough, it's progression; if it's a strategy guide, it's emergent."** Mark Brown ("The Rise of the Systemic Game") popularized this for designers.

## 2.1 Prefer N interacting systems over N×k scripted events

Emergence scales with *interactions between* systems, not with the count of authored content. The corollary (Tom Francis): "once you've built the systems … making a new ability or item is almost trivial."

- **Test for:** count systems S and pairwise interactions I. If I scales toward S² (most systems can affect most others), the design is emergent; if I ≈ 0 (systems siloed, only scripts connect them), it's progression in a systemic costume.
- **Failure mode — The Costume Box:** many "systems" that never touch each other, so all variety is actually authored content and the world feels inert.

## 2.2 Make systems deep (stateful, persistent), not shallow (momentary)

A system that holds and changes state over time produces chains; one that resolves instantly does not. *RimWorld*'s events "cause consequences that last in time, generating new events in connected chains" (Sylvester, GDC 2017).

- **Test for:** each core system exposes persistent state that ≥1 other system reads and mutates. A system whose output is never another system's input is flagged.
- **Failure mode — The Mayfly System:** an interaction that fires and vanishes, leaving no state for others to build on, so no chains form.

**→ Procedural / headless implication.** Volume is the seductive failure for a generator — more content is cheap, more *interactions* is hard. Score the agent on **interaction density, not content count**. A generated region with 200 hand-feeling encounters but no cross-system interaction is worse than 20 that each engage combat, economy, and loot-scarcity at once.

---

# Sub-domain 3 — The "tabletop RPG with a good GM" analogy (and its limit)

> Spector and peers repeatedly invoke the good Dungeon Master: rules keep it a game; the game reacts to the player. Ken Rolston (Morrowind lead): "The goal of every [Elder Scrolls] game is to create something that resembles a pen-and-paper RPG on the computer," with "free-form experience" as the untouchable core. **The founding anecdote:** Spector watched a tester send *Sherry the Mouse* under a simulated portcullis (a real gap existed in the sim) to flip a lever — an unforeseen solution the *system*, not the designer, validated. **The honest limit:** Spector is candid that "you have no idea how hard it is to create a virtual dungeon master" — conversation/improv systems haven't kept pace.

## 3.1 Simulate obstacle state deeply enough that more than one system can resolve it

Don't gate progress on a single intended key. The portcullis worked because the "gap at the bottom" existed in the simulation, so a small party member could pass where a human couldn't.

- **Test for:** every progress gate has ≥2 *systemically* valid solutions (not 2 scripted branches). Enumerate solution paths; a single-solution gate is flagged unless explicitly marked a hard-authored bottleneck.
- **Failure mode — The Telekinesis Lock:** a gate that admits only the one solution the designer imagined, collapsing the immersive sim into a lock-and-key adventure.

## 3.2 Where the virtual GM can't reason, fail toward player freedom, not a scripted rail

Since no system improvises like a human GM, design so unanticipated actions resolve through general rules rather than hitting a wall. *Breath of the Wild* "largely dodged" the missed-trigger problem: "it was okay if players missed things — almost nothing is required to progress."

- **Test for:** for a sample of out-of-distribution player actions, the system produces a *lawful* (rule-consistent) result rather than a null/locked state. No "the designer didn't think of that, so nothing happens" outcomes.
- **Failure mode — The Empty GM Chair:** the player tries something reasonable, the absent virtual GM has no rule for it, and the game silently refuses.

**→ Procedural / headless implication.** An AI-authored content engine is, in effect, an attempt to build the virtual DM Spector says doesn't exist. The *safe* version is **not** a generator that improvises new rules at runtime (that reintroduces inconsistency) — it's one that authors content *within* a fixed, deeply-simulated rule set, so the simulation validates emergent solutions. Let the DM analogy guide *content* (situations, factions, motivations); keep the rule substrate frozen.

---

# Sub-domain 4 — Multiplicative vs. additive design: affordances, not solutions

> Nintendo's "chemistry engine" (GDC 2017, *Breath of the Wild*): a rule-based state calculator alongside the physics engine, with three rules — (1) elements change a material's state; (2) elements change another element's state (water douses fire); (3) materials don't change other materials' state. Dohta called the result **"multiplicative gameplay … found in the space between situations and goals."** *Tears of the Kingdom* (GDC 2024) went further: an *entirely physics-driven* world where "unique interactions happen without any dedicated implementation" — even sound emerged from physics (no dedicated boat sound).

## 4.1 Author orthogonal affordances and let them combine — never enumerate the combinations

Give the player capabilities and the world properties; the product space *is* the gameplay. BotW's "fan a bomb to propel it" or "cut down a tree to cross a gap" weren't scripted — they fall out of fire/wind/physics interacting. TotK's Ultrahand/Fuse let players bind arbitrary objects (the system even auto-names creations).

- **Test for:** capabilities and world properties live in separate tables; the design never enumerates specific capability×property "intended combos" as content. A list of "approved combinations" is additive design masquerading as multiplicative.
- **Failure mode — The Recipe Book:** the designer pre-authors the allowed combinations, so players only rediscover the designer's list instead of inventing.

## 4.2 Base rules on real-world expectation so they're learnable without tutorials

Lower the teaching cost so the multiplicative space is actually usable: "rocks roll downhill, lightning is conducted by metal, fire burns wood, wind pushes things … trust players to learn through exposure."

- **Test for:** each base rule maps to a one-sentence real-world intuition; rules that need in-game tutorialization to be guessed are flagged as friction.
- **Failure mode — The Arbitrary Alchemy:** rules that defy intuition (wood conducts electricity but metal doesn't), forcing rote memorization and killing intention.

## 4.3 Add a system only if it multiplies with existing ones

A new system that merely sits beside the others is additive cost, not multiplicative value. TotK *purged* non-physics objects — anything that couldn't participate in the rule set was removed, not tolerated.

- **Test for:** before adding system S, verify S reads from and writes to ≥2 existing systems. If S only adds content variety, reject or redesign it.
- **Failure mode — The Bolt-On:** a feature (e.g., a standalone fishing minigame) that interacts with nothing else, diluting the systemic core and the teaching budget.

**→ Procedural / headless implication.** Multiplicative design is the single highest-leverage principle for a procedural game: the generator's job becomes *placing affordances* (a flammable thatch roof, a metal grate over water, a token-vendor near a guarded vault) rather than *authoring outcomes* — the combinatorial play is then free. Hand-author the rule table and material-tag taxonomy; once frozen, the generator can scatter tagged objects safely. Danger: a generator that "helpfully" adds bespoke interactions reintroduces exceptions and breaks learnability (4.2). **Constrain generation to tag-assignment, never rule-creation.**

---

# Sub-domain 5 — How emergence and procedural generation multiply each other

> Kate Compton's **oatmeal problem** ("So you want to build a generator…", 2016): "I can generate 10,000 bowls of oatmeal … mathematically unique … but the user will just see a lot of oatmeal. **Perceptual uniqueness** is the real metric." She drew the lesson from Spore and foresaw the same backlash for No Man's Sky (18.4 quintillion unique-but-samey planets). The antidote: generated content becomes meaningful only when *interpreted through interacting systems* — plus *apophenia*, the human tendency to knit random events into stories (RimWorld). (See `procedural-generation` for the full treatment.)

## 5.1 Generate inputs to systems, not finished experiences

Procedural content should be raw material the systems chew on, so variety is perceived through consequence. RimWorld generates colonists, traits, events; the *interactions* (a pyromaniac during a heat wave next to the ammo dump) create perceived uniqueness, not asset variety.

- **Test for:** each class of generated content is consumed by ≥1 system that changes the player's options because of it. Purely cosmetic generated content (no system reads it) is oatmeal — flagged.
- **Failure mode — The Oatmeal Bowl:** numerically unique generated content that no system reacts to, so the player perceives sameness.

## 5.2 Freeze a consistent rule substrate before generating — never generate the rules

Coherence of AI-authored content requires a fixed lawful world for the content to obey. Both Zelda talks hammer consistency ("very few exceptions") as the precondition for emergent solutions.

- **Test for:** the generator's I/O contract shows it consuming a *versioned, read-only* rule set and emitting only rule-compliant artifacts. Any code path that mutates the rule set is a critical violation.
- **Failure mode — The Shifting Physics:** generated regions that each invent their own rules, destroying the player's transferable mental model and with it all emergence.

## 5.3 Tune generation for perceptual uniqueness via systemic salience, not parameter variance

Make generated things *matter differently*, not just *look different*. (Emily Short's gloss on Compton: the hard part is making differences "be perceived as truly different in any memorable way.")

- **Test for:** a blind playtest proxy (or heuristic) can name a *consequential* difference between two instances, not just a cosmetic one. If differences are only stat/skin deltas, raise systemic salience.
- **Failure mode — The Radiant Quest Trap:** endless generated quests that are structurally identical ("kill X at Y"), perceptually interchangeable, systemically inert.

**→ Procedural / headless implication — the synthesis.** Each signature mechanic of a procedural, finite-loot, high-lethality game is a systemic invariant the generator must treat as a *hard constraint*, and each is where oatmeal or exploit risk concentrates:
- **Finite legendary loot** = a global conservation invariant. Never instantiate legendaries from a generic drop table; place them so systemic context (a lethal guardian, a token cost, an irreversible choice) makes each perceptually unique. *Test:* legendary count across the world ≤ the authored cap, enforced as a constraint, not a probability. (See `rpg-systems` C6.)
- **Single-save high-lethality** raises the stakes of perceivable consequence (1.2) and the empty-GM-chair failure (3.2). *Test:* every lethal outcome traces to a player-perceivable cause the rules exposed beforehand — no un-telegraphed deaths. (See `combat-design`, `permadeath-and-lethality`.)
- **Diegetic token economy** = a persistent stateful system (2.2) that should read from and write to combat, loot, and faction systems so tokens form chains (spend → weaken guard → access vault → finite legendary). *Test:* tokens are an input to ≥2 other systems, not a standalone currency. (See `rpg-systems` B4.)
- **AI-authored content engine** = the virtual-DM ambition (Sub-domain 3): safe only as a within-rules content composer over a frozen substrate; unsafe as a runtime rule inventor.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| The Fabergé Object | A scripted interaction that works on one hand-placed object | Universal `tags × rules` behaviors (1.1) |
| The Invisible Lever | World reacts but player can't perceive it | Perceivable consequence per verb (1.2) |
| The Costume Box | Many "systems" that never touch each other | Drive I toward S² interactions (2.1) |
| The Mayfly System | Interactions fire and vanish, no chains | Stateful, persistent systems (2.2) |
| The Telekinesis Lock | Gate admits only the intended solution | ≥2 systemic solutions per gate (3.1) |
| The Empty GM Chair | Reasonable action → silent refusal | Fail toward lawful resolution (3.2) |
| The Recipe Book | Designer pre-authors allowed combos | Orthogonal affordances, don't enumerate (4.1) |
| The Arbitrary Alchemy | Rules defy real-world intuition | Map rules to real-world expectation (4.2) |
| The Bolt-On | A feature that interacts with nothing | Add a system only if it multiplies (4.3) |
| The Oatmeal Bowl | Unique content no system reacts to | Generate inputs to systems (5.1) |
| The Shifting Physics | Each region invents its own rules | Freeze a read-only rule substrate (5.2) |
| The Radiant Quest Trap | Structurally identical generated quests | Systemic salience over parameter variance (5.3) |

---

## Caveats

- **The Nintendo talks are translated / second-hand.** The GDC 2017 & 2024 Zelda talks were delivered partly in Japanese and are most accessible via journalist write-ups and the GDC Vault video, not a published transcript. Quotes are as rendered by those outlets; the "three rules" and "multiplicative gameplay" are consistent across independent reports.
- **The Spector/Ultima VI anecdote is a recollection, not a contemporaneous record**, told in multiple venues with varying detail; some community sources question the literal portcullis mechanic. The *design lesson* is well-attested; treat the specific detail as illustrative. The "I didn't think of that, but the system allowed it" phrasing is a paraphrase — his actual words: "this is what games should do. We should start planning this."
- **"Emergence cannot be designed" is a contested academic position** (Salen & Zimmerman; Soler-Adillon). The operational takeaway — design and test the *rules*, measure the possibility space — holds regardless of the philosophical stance.
- **Juul's emergence/progression is a spectrum, not a binary.** Most shipping games mix both; the "strategy guide vs. walkthrough" test is a heuristic.
- **Morrowind is an imperfect systemic exemplar** — celebrated for free-form, faction-driven reactivity and a "lived-in" world, but its systemic *interaction* depth is shallower than the Looking Glass/Arkane or Zelda exemplars. Use it for world-reactivity and exploration precedent, not as a multiplicative-systems model.
- **AI-authoring risks the source literature predates.** None of the cited sources (1999–2024, human-authored) address generative-AI authoring directly. The specific failure modes — a generator optimizing a misspecified variety metric into oatmeal, inventing per-object exceptions that break learnability, or violating conservation invariants (finite loot) encoded as soft goals — are well-grounded extrapolations, unproven at the scale of a fully AI-authored open-world RPG. Treat such a project as partly experimental.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
