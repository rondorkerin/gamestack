# Onboarding & Teaching — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for mechanic introduction, tutorial design, and FTUE. Run against a tutorial pass, an onboarding review, or a batch of generated content. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** dependency graph → one mechanic per beat → safe space → staked gate → combination → twist → FTUE arc. Don't introduce combination beats before the constituent mechanics are individually mastered.

---

## A · The four-beat pattern (Introduce → Test → Combine → Twist)

**Do**
- [ ] Introduce each new mechanic in a **consequence-free safe space** — no loss condition, or a trivially reversible one — so all attention goes to observing the mechanic, not surviving.
- [ ] Follow every safe introduction with a **staked gate** solvable only by the just-taught mechanic; close all alternate paths before shipping.
- [ ] Follow the gate with a **combination beat** requiring the new mechanic plus exactly one previously mastered prerequisite — both must have individual prior gates.
- [ ] Deliver a **twist** — the same mechanic applied in an unexpected context — that produces an "aha" rather than confusion, then a conclusion with an unambiguous success signal (door, enemy death, puzzle clear).

**Don't**
- [ ] Don't introduce a mechanic for the first time as a required action with lethal consequences ("ambush introduction").
- [ ] Don't combine mechanics the player has not individually cleared through their own safe→gate sequence.
- [ ] Don't make the twist a new mechanic in disguise — that is a second ambush introduction without a safe-space phase.

**Test for** — Can a new player clear the safe space without damage? Can they *fail* the gated room by avoiding the target mechanic (no alternate path)? Does the combination room require two mechanics, both of which have prior gates? Does the twist require only previously-learned information, not new knowledge?

---

## B · Show-don't-tell (teach through the level, not the popup)

**Do**
- [ ] Design every mechanic's first appearance so a player who **reads nothing** can discover it through action and observation within 60 seconds.
- [ ] Use **affordances** — visual cues signaling function — on every interactive surface before the player touches it (glows, distinctive shapes, contrast).
- [ ] Apply the **demonstrative method** for lethal or irreversible mechanics: show the mechanic operating on an NPC or object before the player must engage.
- [ ] Teach through **consequence**: every first-attempt failure must have a single, visible, immediate cause the player can articulate within 5 seconds.

**Don't**
- [ ] Don't make text the **primary** teaching channel; text may reinforce, never substitute — a player who skips the popup must still understand the mechanic.
- [ ] Don't ship false affordances — elements that look interactive but are not, or environmental elements that look safe but are dangerous.
- [ ] Don't introduce a lethal mechanic with the player already inside it, and no prior opportunity to observe it safely.

**Test for** — Skip-everything playtest: player skips all popups, presses no buttons unprompted. Does every core mechanic get discovered within 60 seconds of first encounter? Remove all text: can new players identify interactive vs. dangerous vs. background? List every instant-kill mechanic — does each have a demonstrative moment?

---

## C · Progressive disclosure & scaffolding (one thing at a time, in dependency order)

**Do**
- [ ] Introduce **no more than one genuinely new mechanic per level segment**. Two requires justification; three is almost always a design error.
- [ ] Deliver each mechanic's tutorial **just-in-time** — at the moment of first need. Flag any gap between instruction and first required use exceeding 5 minutes.
- [ ] Build the **mechanic dependency graph** (DAG) in pre-production; treat introduction-order violations as hard errors, not style choices.
- [ ] **Show the lock before the key**: place at least one visible but currently inaccessible element in the tutorial area; the unlocking mechanic must appear within 15 minutes.

**Don't**
- [ ] Don't front-load mechanics in a pre-game arc — instruction delivered before the player has context for it is forgotten before it is needed.
- [ ] Don't introduce Mechanic C before the player has independently passed Mechanics A and B, if C requires both.
- [ ] Don't make the tutorial area self-contained with no visible content beyond it — removes long-term pull and motivation to learn.

**Test for** — Count new mechanics per level (flag >1 for review, flag >2 as error). Measure instruction-to-first-use gap per mechanic (flag >5 min). Topologically sort the dependency graph; compare to actual introduction order; list any violations. Does the tutorial area contain at least one visible lock?

---

## D · FTUE & early retention (first session arc)

**Do**
- [ ] Reach **genuine player-controlled, in-genre play within 180 seconds** of app launch.
- [ ] Deliver a **small, clear win** with explicit visual/audio feedback before the player's first failure or first obstacle.
- [ ] **Instrument every tutorial step** as a funnel event — recorded playtest pre-launch, analytics event on day one post-launch.
- [ ] Establish a **long-term goal** that is visible but not achievable in this session, within the first 5 minutes.
- [ ] Include at least one **personalization moment** (name, appearance, starting choice) before the first loss condition.
- [ ] Ship a **skip flag** on every tutorial segment so returning or experienced players can bypass it.

**Don't**
- [ ] Don't place account creation, unskippable cutscenes, or terms of service before the first playable moment.
- [ ] Don't begin with a difficulty spike or death-eligible encounter before any success moment ("cold open failure").
- [ ] Don't end the tutorial without establishing what the player does next — goalless tutorials kill session-2 return.
- [ ] Don't launch without tutorial funnel instrumentation — if you can't see the drop step, you can't fix it.

**Test for** — Timestamp first in-genre action (flag >180 s). Does a win moment precede the first failure? Can the player articulate one long-term goal after 20 minutes of play? Is every tutorial step tagged as a funnel event? Does the skip flag exist, work, and go somewhere useful?

---

## Anti-patterns to flag on review

| ❌ Anti-pattern | Trigger | Test |
|---|---|---|
| Unskippable tutorial wall | Mandatory pre-game sequence with no bypass | Is there a "played before?" skip path for every tutorial segment? |
| Teaching the obvious | Instructions describe universal input actions (move, jump, attack) | Read instructions aloud: would a player who has played any game in this genre already know this? Remove it. |
| Mechanic taught but never reinforced | Tutorial introduces a mechanic that disappears from the game | Is every tutorial mechanic required or heavily rewarded within 30 min of post-tutorial play? |
| Information ambush | Multiple new mechanics, UI elements, and narrative hooks in one scene | Does any single beat introduce >1 new mechanic? If so, split it. |
| Punishment during learning | Failure in the safe-space phase carries a consequence persisting beyond the current attempt | List every failure consequence during safe-space phases. Do any persist? Move them to the challenge phase. |

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **Mechanic dependency graph** (topological order) → **prerequisite-order validator**: reject any generated level that introduces a mechanic before its prerequisites are cleared.
- [ ] **Four-beat template per mechanic** (safe / gate / combine / twist) → **beat-completeness validator**: reject mechanic introductions missing any of the four beats.
- [ ] **Affordance fields** on every mechanic definition (`affordance_visual`, `demonstrable_target`, `failure_consequence_visible`) → **affordance validator**: reject any introduction room missing all three signals.
- [ ] **FTUE invariants** (first room winnable + early-win token + long-term goal node visible) → **first-session validator**: reject generated first-session configurations that violate any invariant.
- [ ] **Skip flag** queryable on every tutorial segment → **skip-path validator**: no generated tutorial is a forced-only path to main content.

> Gate every generated tutorial batch through `procgen-review` as well. For gated rooms, run the Green et al. ability-ablation test (arXiv 1807.06734): ablate the target mechanic and confirm the level fails — that is how you verify the gate has no bypass.
