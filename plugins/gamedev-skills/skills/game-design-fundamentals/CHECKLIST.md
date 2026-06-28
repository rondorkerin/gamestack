# Game-Design Fundamentals — Checklist

Actionable **Do / Don't** + **Test-for** criteria. These are written to be enforced as **automated invariants** in a generation loop, not just read once. See `GUIDE.md` for reasoning and sources.

---

## Interesting decisions

**Do**
- [ ] Ensure **no option in a choice set is strictly best** across reachable states.
- [ ] Make each significant choice produce a **perceivable, persistent** state change.
- [ ] Surface **cost, stakes, and reversibility** before the player commits.
- [ ] Give every interesting decision a **quantified trade-off** (something given up).

**Don't**
- [ ] Don't ship choices with a dominant option, a free lunch, or an unforeseeable "gotcha."
- [ ] Don't assume a decision that's interesting once stays interesting on repeat.

**Test for**
- [ ] Monte-Carlo each generated choice set; flag any option a competent solver picks **>80%** of the time (dominant-strategy candidate). **Threshold:** if >5% of choice sets fail, fix the *system*, not the instances.
- [ ] Each "interesting" decision has a world-state diff at T+N that is surfaced to the player.
- [ ] No interesting decision is a pure gain (no cost).

---

## Flow & difficulty

**Do**
- [ ] Keep estimated **challenge within a band around modeled skill**; raise both over time.
- [ ] **Teach one mechanic at a time**, isolated and low-stakes, before combining/escalating.
- [ ] If using DDA, **bound and log it**; decide deliberately hidden vs. visible.

**Don't**
- [ ] Don't spike difficulty or leave long boredom troughs.
- [ ] Don't introduce a mechanic for the first time in a lethal context.
- [ ] Don't let rubber-banding nullify skill, or use DDA to mask bad encounters.

**Test for**
- [ ] Rolling |challenge − skill| stays under threshold; flag sustained excursions. Validate the **sequence** of encounters, not just each one.
- [ ] Each mechanic has (i) an isolated first appearance, (ii) a low-stakes practice, (iii) a later combined instance; first appearance is never lethal.
- [ ] DDA (if any) has min/max caps and never silently inverts player effort.

---

## Rewards & motivation

**Do**
- [ ] Map **every reward to a need**: competence (mastery), autonomy (options), or relatedness (bonds).
- [ ] Keep ≥2 viable goal paths available at most points; maintain ≥1 persistent relational thread.
- [ ] Bound all RNG rewards with **pity timers / caps**.

**Don't**
- [ ] Don't build rewards that map only to "retention" (Skinner-box drift).
- [ ] Don't over-reward an already-fun activity until it feels like work (overjustification).
- [ ] Don't gate **core progression** behind a variable-ratio drop.

**Test for**
- [ ] Each reward maps to a player accomplishment or expands meaningful options; flag pure time-drip with no skill correlate.
- [ ] No *mandatory* gate depends on RNG beyond a defined patience budget; a deterministic fallback exists.
- [ ] At any save/quit point, ≥1 objective is **>50% complete and visibly surfaced**; goals are nested (≥2 levels) with offset completion times.

---

## Feedback & agency

**Do**
- [ ] Emit **immediate, legible feedback** for every interactive verb (audio + visual, ~100 ms).
- [ ] Give every "meaningful" choice a real downstream **state delta**.
- [ ] Show **visible progress** for every long-horizon goal, traceable to player action.

**Don't**
- [ ] Don't let any commit-action pass with a silent frame — or drown signal in juice overload.
- [ ] Don't present cosmetic, reconverging branches with false stakes language.
- [ ] Don't leave progression opaque (player can't tell if/why they advanced).

**Test for**
- [ ] 100% of verbs have a 2-channel feedback contract — a mute verb is a build-breaker.
- [ ] Every choice tagged "meaningful" has a measurable diff; zero-delta choices are re-tagged "cosmetic."
- [ ] (Lethal games) every lethal attack has a generated **telegraph** before it lands.

---

## Choice architecture

**Do**
- [ ] **Cap simultaneously-presented meaningful options**; scale the cap with measured mastery, not playtime.
- [ ] **Prune** options that are Pareto-dominated, duplicative, or inconsequential — be ruthless.
- [ ] (Multiplayer/asymmetric) prevent kingmaker and runaway-leader end-states.

**Don't**
- [ ] Don't maximize option *count* — players experience *perceived* variety, not combinatorics.
- [ ] Don't keep options that add cognitive cost without decision value.

**Test for**
- [ ] Simultaneous meaningful options at any node ≤ the tuned cap; flag nodes that exceed it.
- [ ] No choice set contains Pareto-dominated or functionally identical options.
- [ ] (Multiplayer) no zero-win-probability agent can deterministically pick the winner; no leader is mathematically uncatchable before the end.
- [ ] Generation budget favors **a few perceptually-distinct artifacts** over many near-identical ones (route to `procgen-review`'s oatmeal/fanfic tests).
