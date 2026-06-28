# Game-Design Fundamentals — Guide

The foundational principles, with sources, exemplars, failure modes, and — because this pack targets headless/procedural generation — the implication for an AI authoring engine. Pair with `CHECKLIST.md`.

> **A note on enforcement:** a human team feels when a game is unbalanced, grindy, or unfair. An autonomous generator does not. Every principle below should be wired up as an *automated invariant* the engine checks before content ships — not a guideline it's trusted to follow.

---

## 1. Interesting decisions (Sid Meier)

**Source:** Sid Meier, "Interesting Decisions," GDC 2012 (the phrase dates to his GDC 1989 "a game is a series of interesting decisions"). Meier's anatomy of an interesting decision: trade-off / opportunity cost · situational · persistent · expresses play-style · risk vs. reward · short- vs. long-term · supported by enough information · and the rule that *something interesting once isn't necessarily interesting repeated*.

### 1.1 Eliminate dominant options — the highest-leverage rule
A choice with one strictly-best answer is not a decision; it's a speed bump. If players always pick the same option (or pick at random), the choice may as well not exist.

- **Exemplar:** Civilization's **Infinite City Sprawl** — early Civ penalized city *size* but not *count*, so spamming tiny cities was strictly optimal. Later titles added spacing rules and per-city penalties to break it. The canonical "dominant strategy patched out."
- **Failure mode:** *dominant strategy* (and its sprawl form, ICS).

### 1.2 Consequences must be believed and persistent
Each significant choice must change future state in a way the player can perceive, and that lasts. Meier: interesting decisions "are persistent and affect the game for a certain amount of time."
- **Failure mode:** *fake choice / illusory agency* (see §4).

### 1.3 Inform the choice
Surface cost, relevant state, and the likely consequence class *before* the player commits. Meier deliberately uses familiar framings (history, zombies) so players bring knowledge, and advises "erring on the side of providing the player with too much information."
- **Failure mode:** *gotcha decision* — a consequence the player could not have anticipated; and the "Mr. Bubble Boy" spiral where one unforeseeable setback colors the whole experience.

### 1.4 Force a trade-off
Every interesting decision should make the player give something up — a quantified resource or opportunity cost. A pure gain is not a decision.
- **Failure mode:** *no-brainer / free lunch.*

> **Procedural implication.** A generator composes systems no human balanced, so it *multiplies* the risk of accidental dominant strategies. Generate constraints and solvers, not just content: every time the engine emits a choice set (loot table, build option, faction reward), run a dominance check before it ships. With **finite legendary loot**, a single legendary that strictly super-sets others in its slot becomes a *permanent* dominant option that breaks the whole economy — verify against this, and hand-author the legendary backbone while the generator fills commons.

---

## 2. Flow & difficulty curves

**Sources:** Csikszentmihalyi, *Flow* (1990) — the challenge/skill three-channel model (boredom / flow / anxiety). Jesse Schell, *The Art of Game Design* — Lens of Flow (clear goals, no distractions, direct feedback, continuous challenge).

**Macro vs. micro flow:** *micro-flow* is the second-to-second challenge≈skill match inside an encounter; *macro-flow* is the session/campaign arc. Keep both in-channel; a **sawtooth** (rising tension, periodic release) sustains macro-flow while micro-flow handles the moment.

### 2.1 Keep challenge inside the flow channel
Hold estimated challenge in a band around modeled player skill, widening tolerance as skill grows. Note (Carli et al.): low-challenge/low-skill yields *apathy*, not flow — raise **both** over time.
- **Failure mode:** *difficulty spike* (challenge ≫ skill) and the *boredom/apathy trough* (challenge ≪ skill).

### 2.2 Teach one mechanic at a time, in a safe space, before testing it
Introduce each mechanic isolated and low-stakes, let the player practice, then combine and raise stakes.
- **Exemplar:** **Super Mario Bros. World 1-1** — Goomba on flat ground, pipes of rising height to teach variable jump, solid ground (not pits) between early obstacles so failure costs little. Almost everything later builds on what 1-1 teaches.
- **Failure mode:** *mechanic dump / tutorial wall.*

### 2.3 Adjust difficulty honestly
If you auto-adjust (DDA), decide deliberately whether it's hidden or visible, bound it, and never let it punish success in a way players resent.
- **Exemplars:** Resident Evil 4's hidden 1–10 difficulty scale; Left 4 Dead's "AI Director" modulating spawns by stress; God Hand's *visible* meter; Mario Kart rubber-banding (overt, sometimes resented).
- **Failure mode:** *rubber banding* that nullifies skill; *DDA masking design flaws.*

> **Procedural implication.** A generator can't *feel* a spike — it must *measure* one. Attach a challenge estimate to every generated encounter and validate the **sequence**, not just each piece (a string of individually-fine rooms can still form a spike or a flat trough). Hand-author the teaching order of core mechanics — never let the generator decide *when a mechanic is first taught*. Under **single-save high-lethality**, the channel is narrow and an anxiety excursion is catastrophic: an unannounced procedural spike isn't "fun pain," it's a cheap death. Guarantee a gated skill ramp and telegraph lethal encounters.

---

## 3. Reward schedules & motivation

**Sources:** Ryan, Rigby & Przybylski (2006), *Motivation and Emotion* — Self-Determination Theory applied to games; the three needs **competence, autonomy, relatedness** (foundation of the PENS framework, *Glued to Games*, 2011). Overjustification: Deci, Koestner & Ryan (1999) meta-analysis of 128 studies. Operant background: Skinner; John Hopson, "Behavioral Game Design" (2001).

**Intrinsic vs. extrinsic; immediate vs. projected.** Intrinsic motivation comes from need satisfaction (mastery = competence, meaningful choice = autonomy, bonds = relatedness). Extrinsic comes from external rewards (loot, XP, currency). Immediate rewards close the moment-loop; *projected* rewards sustain sessions.

### 3.1 Design rewards to signal competence, not merely to bribe
Prefer rewards that confirm mastery / expand meaningful options over rewards that exist only to reinforce grinding. The SDT studies found competence and autonomy — not content or narrative — drove enjoyment and continued play.
- **Failure mode:** *Skinner box / compulsion loop*; the *overjustification effect* (over-rewarding an already-fun activity until it feels like work). **Bound:** undermining is strongest for *expected, tangible, performance-contingent* rewards (d ≈ −0.28 to −0.40); informational praise and *unexpected* rewards don't undermine and can enhance — so don't strip all extrinsic reward.

### 3.2 Use variable rewards sparingly and transparently
Variable-ratio is the most extinction-resistant schedule (Skinner) — powerful and ethically loaded (loot boxes, slot machines). Bound it; never make *core* progression hostage to it.
- **Failure mode:** *grind wall / RNG gate*; predatory *compulsion loop*. Mitigation: pity timers/caps; deterministic fallback for any required drop.

### 3.3 Build projected goals ("one more turn")
Always leave a salient, near-complete next objective; nest long-term goals into overlapping short-term ones with **staggered** completion so a reward is always about to land.
- **Exemplar:** Civilization desynchronizes sub-goal completion so there's always a next step. (Caveat: critics note "one more turn" can be a content-agnostic pull, not proof of quality.)
- **Failure mode:** *dead air* (all goals done or all far away); *engagement-for-its-own-sake.*

### 3.4 Satisfy autonomy and relatedness, not just competence
Provide ≥2 viable goal paths at most points (autonomy) and ≥1 persistent relational thread — faction/NPC/companion (relatedness).
- **Failure mode:** *single golden path* (autonomy denial); *empty world* (relatedness vacuum).

> **Procedural implication.** Procedural reward generation is where Skinner-box drift happens *silently*: an optimizer told to "maximize retention" will rediscover variable-ratio exploitation on its own. Constrain the generator with explicit need-satisfaction objectives and hard caps on RNG gating. A **diegetic token economy** is the lever — because tokens are in-world objects, generate sources/sinks that *signal competence and expand autonomy* (earned by demonstrated skill, spent on meaningful capability) rather than time-drip faucets. **Finite legendary loot** naturally resists the compulsion loop, but raises a fairness risk: the generator must guarantee no player is soft-locked out of a *viable* build because finite legendaries were placed unreachably.

---

## 4. Feedback & agency

**Sources:** Meier (GDC 2012) on feedback; Jonasson & Purho, "Juice It or Lose It" (GDC Europe 2012); Folmer Kelly (2014) on polish-over-context; Telltale / illusion-of-choice analyses.

### 4.1 Acknowledge every player action immediately
No decision or input passes without immediate, legible feedback. Meier: "The worst thing you can do is just move on… at least have a sound effect that says 'I've heard what you said.'" Juice = screenshake, particles, easing, sound, permanence (debris, corpses).
- **Failure mode:** *silent input / dead feedback* — and its overcorrection, *juice overload* (polish drowning context; both none and extreme juice hurt experience).

### 4.2 Make agency real; if you fake it, mask it well and rarely
Branches presented as meaningful must alter state. Reconverging "choices" must be framed honestly as flavor, not stakes. Telltale's *The Walking Dead* funnels branches to a shared mainline — it works only because the writing is masterful and time-pressured; perceived agency collapses the moment players detect reconvergence.
- **Failure mode:** *fake choice / illusory agency*; *save-scumming pressure* (reversible high-stakes choices that invite reload-optimization).

### 4.3 Make progression legible and attributable to the player
Show progress toward goals; tie visible gains to player action so competence is *felt*. Every long-horizon goal needs a visible indicator; gains traceable to an action.
- **Failure mode:** *opaque progression.*

> **Procedural implication.** Generators are strong at branches but weak at *consequence* — exactly the illusory-agency trap at scale: cheap to emit 1,000 choices, expensive to make each matter. Require the engine to register a concrete state mutation for every generated choice, or down-rank it to cosmetic (don't present cosmetic choices with stakes language). Generate a **feedback contract** per verb (audio + visual + permanence) so AI-spawned mechanics never ship mute. Under single-save lethality, **telegraph generation is mandatory output, not decoration** — clear pre-attack feedback is what converts a death from "cheap" into "fair."

---

## 5. Analysis paralysis & choice architecture

**Sources:** Iyengar & Lepper (2000), the jam study. Contested by Scheibehenne et al. (2010) meta-analysis (effect near zero overall) and Chernev et al. (2015), which isolate *moderators* (time pressure, set complexity, decision difficulty, preference uncertainty) that determine when overload bites. Kingmaker/runaway framing: Pulsipher, "The Three-Player Problem."

**The jam study, precisely:** a tasting booth showing 6 vs. 24 jams. The 24-jam display drew more passers-by (60% vs. 40% stopped), but only **3% (4 customers)** of those who stopped bought, vs. **30% (34)** at the 6-jam booth — ~10× conversion gap. *Caveat:* the general effect is weak in meta-analysis; it bites mainly under time pressure / complexity — which is most action contexts, but not all.

### 5.1 Bound the active choice set; stage complexity
Limit simultaneously-presented *meaningful* options; reveal more as skill/context grows. Meier warns that complex decisions "one after the other" make players "feel out of control."
- **Failure mode:** *choice overload / analysis paralysis*; *decision fatigue.*

### 5.2 Prune trivial and redundant choices
Cut options that are strictly worse, duplicative, or inconsequential. Meier reports ~⅓ of attempted features are cut. "Be ruthless."
- **Failure mode:** *bloat* (options that add cognitive cost without decision value).

### 5.3 Avoid degenerate end-states (multiplayer / asymmetric)
Prevent states where a non-winning actor decides the winner (*kingmaker*) or a leader is uncatchable (*runaway leader*). Mitigations: hidden victory points, uncertainty about who's leading, simultaneous reveals.
- **Failure mode:** *kingmaker*, *runaway leader*, *turtling*, *leader-bashing.*

> **Procedural implication.** A generator's instinct is to maximize quantity; players experience *perceived* variety. This is Kate Compton's **"10,000 bowls of oatmeal"** — mathematically unique, perceptually identical. Optimize for **perceptual uniqueness** (Compton's heuristic: "would you write fanfic about it?") over combinatorial count: spend the generation budget on a *small number of distinct* artifacts, not a vast number of near-identical ones. Cap meaningful options surfaced at once (e.g. limit simultaneous sink options at a vendor/altar). Finite legendary loot is the antidote to oatmeal at the top of the curve — hand-curate the legendaries for uniqueness, generate the perceptually-differentiated commons beneath. *(Full treatment: `procedural-generation`.)*

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Dominant strategy / ICS | One option always chosen | Dominance-scan choice sets; add trade-offs |
| No-brainer / free lunch | Choice with no cost | Attach a quantified opportunity cost |
| Gotcha decision | Unforeseeable consequence | Surface stakes/reversibility before commit |
| Difficulty spike | Sudden challenge ≫ skill | Validate encounter *sequences*; telegraph |
| Boredom trough | Challenge ≪ skill for long | Raise both challenge and skill |
| Mechanic dump | Many systems before any skill | Teach one at a time, low-stakes first |
| Skinner box | High retention, low satisfaction | Map rewards to competence/autonomy |
| RNG gate | Required drop behind variable-ratio | Pity timer + deterministic fallback |
| Dead air | No near-complete next goal | Nest staggered projected goals |
| Fake choice | Branches reconverge, no state delta | State-delta audit; re-tag as cosmetic |
| Silent feedback / juice overload | Mute verbs, or polish drowning signal | 2-channel feedback contract; tune the middle |
| Analysis paralysis | Players stall at choices | Cap simultaneous options; prune redundancy |
| Oatmeal | Vast but perceptually samey | Perceptual uniqueness over count (`procgen-review`) |

---

## Sources & caveats

Practitioner talks (Meier, Jonasson/Purho, Compton) are expert heuristics, not controlled studies. Choice-overload is contested — apply §5.1 where time pressure/complexity exist. Overjustification is bounded to expected/tangible/performance-contingent rewards. The flow three-channel model is a compass, not an instrument (low/low = apathy). RE4's hidden difficulty grades are community-attested, not first-party. **None of these sources anticipates a generator that can silently rediscover dominant strategies and Skinner-box schedules, flood the player with oatmeal, or emit thousands of fake choices cheaply** — which is exactly why this skill's tests are written to be enforced automatically.

*Expand with your own telemetry and citations — see CONTRIBUTING.md.*
