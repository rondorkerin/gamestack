# Difficulty & Balancing — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for balance and difficulty in any game system. Run against a balance pass, a new content ship, or a batch of generated content. Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** declare balance objectives in writing → build the cost-curve spreadsheet → run dominance checks → design the difficulty curve → wire DDA → instrument telemetry. Balancing content you can't measure is intuition-only and breaks at scale.

---

## A · Dominant strategies & degenerate cases

**Do**
- [ ] Run a **dominance check** on every option pool before ship: compute EV across representative game states; flag any option with EV > all others in ≥80% of samples.
- [ ] Audit option viability with Sirlin's **five-tier model** (God / Strong / Fair / Weak / Garbage); populate only Strong / Fair / Weak.
- [ ] Apply stricter dominance thresholds for **multiplayer** (one strategy winning ≥60% of agent matches = immediate investigation).

**Don't**
- [ ] Don't ship a content batch without a dominance check — especially from a generator that can emit thousands of options per run.
- [ ] Don't tolerate a top tier containing <20% of options but >70% of wins.

**Test for** — Does any single option's pick rate exceed 75% of all filled slots in playtesting? Does any strategy win ≥60% of AI-tournament matches? Both are immediate flags.

---

## B · Balance structures: symmetric, asymmetric, intransitive, cost-curve

**Do**
- [ ] For **transitive systems** (weapons, units, cards): build a balance spreadsheet; normalize all effects to one resource unit (DPS, effective HP, resource-per-turn); flag options outside ±2σ of same-tier mean.
- [ ] For **intransitive systems** (factions, deck archetypes, unit triangles): build a payoff matrix; verify every option beats at least one other and loses to at least one other; verify a mixed-strategy Nash equilibrium with positive probability mass on all options.
- [ ] For **asymmetric starting options**: run N=1000 simulated matches per pair at equal skill; win rates outside 45–55% → investigate; outside 40–60% → patch.
- [ ] Apply **Rosewater's mana-cliff discipline**: costs above a threshold tier should buy dramatically higher per-unit effect to compensate opportunity cost.
- [ ] Give complex options **lenticular depth**: simple face value for beginners, hidden strategic upside for experts — and verify the conditional upside doesn't push the option above the cost curve when the condition is easy.

**Don't**
- [ ] Don't add an option to the game without adding it to the balance spreadsheet first.
- [ ] Don't conflate transitive and intransitive balance — use payoff matrices for cycles, cost-curve math for linear progressions.
- [ ] Don't allow power creep: each content addition must keep the global average benefit-per-cost constant, not shift it up.

**Test for** — Does the balance spreadsheet cover 100% of options in each transitive system? Does the payoff matrix for each intransitive system show no weakly dominant row? Do any options fall outside ±2σ? Do any options have 0 probability mass in the Nash equilibrium?

---

## C · Difficulty curves & dynamic difficulty adjustment

**Do**
- [ ] Plot a **challenge estimate** (enemy HP × count × speed, or equivalent) for every encounter in sequence. Verify: trend line rises; variance creates visible peaks and troughs; no single encounter exceeds 2.5× session-average without a preceding teaching encounter.
- [ ] Include a **lull** (challenge < 0.7× session-average) at least once every N encounters.
- [ ] Before implementing DDA: write down what it measures, what it changes, how fast it changes, and whether the player sees it.
- [ ] For **cooperative DDA** (L4D Director model): track per-player health/stress separately; insert per-player lulls; verify every chapter contains ≥1 identifiable peak and ≥1 identifiable lull in telemetry.
- [ ] Write a **player-facing rationale** for any hidden DDA ("the world responds to you") and verify the system cannot be exploited by deliberate underperformance.

**Don't**
- [ ] Don't ship a monotonically rising difficulty curve — both fatigue and anxiety spikes result.
- [ ] Don't adjust global parameters without per-player stress tracking in cooperative games (one bad player trivializes for the whole team).
- [ ] Don't call RE4's system "fear AI" — the term is not used in any Capcom document; the system is a 1–10 hidden difficulty score.
- [ ] Don't implement rubber-band DDA so aggressively that skilled play is immediately negated (breaks skill-outcome correlation).

**Test for** — Is there a challenge-estimate time series for every encounter? Does any single encounter exceed 2.5× session-average without a preceding easier encounter? In cooperative DDA telemetry: does every chapter show ≥1 peak and ≥1 lull? Can a player exploit the hidden DDA system by deliberately underperforming?

---

## D · Difficulty settings & accessibility

**Do**
- [ ] List every distinct **skill axis** the game challenges (reaction time, resource management, spatial reasoning, memorization, build complexity). For each axis, implement a corresponding assist that reduces difficulty on that axis without nullifying others.
- [ ] Name difficulty options with **neutral descriptors** of what they do ("Exploration Mode," "Reduced Enemy Damage") not what they say about the player ("Easy Mode," "Baby Mode").
- [ ] For single-player assists: verify they have zero effect on other players' experiences (no multiplayer bleed).
- [ ] If no difficulty assists exist: publicly state the design intent; verify the game is demonstrably completable through in-game mechanics by the target audience without hardware modification.

**Don't**
- [ ] Don't bundle multiple skill axes into one mode — "Easy Mode" that simultaneously reduces reaction windows, damage, and puzzle complexity is inaccessible to players who only need help on one axis.
- [ ] Don't apply single-player assists to ranked or competitive modes.
- [ ] Don't use "difficulty as design intent" to avoid engineering accessibility work when the actual difficulty comes from unfixed jank rather than deliberate design.

**Test for** — Is there one assist per distinct skill axis? Does any assist label imply judgment of player skill? Do single-player assists affect multiplayer or ranked modes? Is the game completable by the target audience without any assists on the declared canonical difficulty?

---

## E · Metrics-driven balance: spreadsheets, simulation, telemetry

**Do**
- [ ] Maintain a **balance spreadsheet** covering 100% of options in every transitive system; update it before every content ship.
- [ ] Define **telemetry action thresholds in writing before launch**: pick rate ≥75% → investigate dominant strategy; boss abandonment ≥60% → tuning emergency; faction win rate outside 45–60% at high bracket → asymmetric balance problem; session length drop ≥20% after encounter → difficulty spike.
- [ ] If using self-play simulation: use **population-based training** (diverse agent pool) to prevent strategy collapse; reward diverse winning strategies, not just winning.
- [ ] Write a **player-facing justification** for every DDA parameter adjustment ("this makes your experience better because..."). If the honest answer involves monetization pressure, redesign the system.

**Don't**
- [ ] Don't track only vanity metrics (total playtime, DAU) without per-option pick rates, win rates, and abandonment rates.
- [ ] Don't treat self-play RL as production-ready for balance validation without significant tooling investment — it is research-stage as of 2026.
- [ ] Don't tune DDA against player interests to create spending pressure; this is both an ethical and reputational risk.
- [ ] Don't run a single-agent self-play loop — two agents converging on the same counter-strategy produces a degenerate equilibrium that appears "balanced."

**Test for** — Does the balance spreadsheet exist and cover 100% of transitive-system options? Are telemetry action thresholds written down before launch? If using self-play, does the agent pool explore all major strategy archetypes? Can every DDA adjustment be justified to the player without mentioning monetization?

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — define before generating, validate every batch, **block commit** on failure:

- [ ] **Dominance invariant**: no generated option has EV > all others in >80% of game states → **dominance-check gate** (reject and regenerate with variance penalty).
- [ ] **Cost-curve invariant**: every generated option's benefit-to-cost ratio falls within ±1σ of the same-tier mean → **cost-curve linter** (flag ±1σ; reject ±2σ).
- [ ] **Payoff-cycle invariant**: every intransitive system has a verified payoff matrix with no weakly dominant row and positive Nash mass on all options → **payoff-matrix validator**.
- [ ] **Challenge-sequence invariant**: no encounter exceeds 2.5× session-average without a preceding easier encounter; every N encounters include ≥1 lull at <0.7× session-average → **encounter-sequence validator** (hard gate for single-save high-lethality games; see `permadeath-and-lethality`).
- [ ] **Closed-loop auto-tuning**: every generated encounter is instrumented with a predicted challenge score at generation time; actual vs. predicted discrepancy drives bounded parameter updates (no more than ±X% per session, range capped) → **telemetry feedback loop**.
- [ ] **Per-axis difficulty scalars**: difficulty is implemented as multipliers on generated parameters, not as separate generation paths → **single-path generator with scalar layer**.

> For the **procedural encounter gating** application of these guardrails, apply `procgen-review` on top of this skill. For fairness constraints under permanent death, apply `permadeath-and-lethality`.
