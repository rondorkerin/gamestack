# Permadeath, Lethality & Meta-Progression — Checklist

Actionable **Do / Don't** + **Test-for** for high-lethality / permadeath design. Run against an encounter, a death/restart loop, a meta-progression economy, or a long-form persistence plan. See `GUIDE.md` for reasoning and sources. **Areas 1–3 rest on shipped games; ⚠️ Area 4 is extrapolation — playtest everything.**

---

## Fairness (Area 1) — the non-negotiable backbone

**Do**
- [ ] Make every death **attributable to a player decision** they could have made differently with available information.
- [ ] Give every lethal hazard **≥1 pre-damage signal** AND **≥1 reachable counter-action**.
- [ ] Scale the **telegraph window to lethality** — deadlier attacks get longer/clearer tells.
- [ ] Verify readability at **generated** camera distances and lighting (dark procedural interiors).
- [ ] Use **deterministic or transparent** randomness for lethal outcomes; surface true odds.

**Don't**
- [ ] Don't ship off-screen / unsignaled / un-haveable-information deaths (fake difficulty).
- [ ] Don't ship 1-frame unreadable lethal attacks.
- [ ] Don't let a single unsurfaced roll end a run with no player-controllable mitigation.
- [ ] Don't surface a probability then violate it with hidden modifiers.

**Test for** — Does every lethal hazard have a signal + counter? Can a competent agent survive each **generated** encounter with the information available (automated survivability pass)? No dominant option ≥~85% at any decision node?

---

## Mitigations (Area 2) — cut cost, keep stakes

**Do**
- [ ] Keep the **failure→restart loop short** relative to total content; cost = replayable variety, not repeated identical content.
- [ ] Offer **opt-in, judgment-free, stacking** difficulty assist (e.g. 20%→80% damage resistance) that keeps death and stakes.
- [ ] Pick **one** anti-save-scum model and enforce it (single-save / deterministic seed / hyper-RNG / consequence-delay).
- [ ] Shape difficulty as **earned, transparent escalation** tied to mastery or opt-in; scale reward with risk.
- [ ] Make **every death teach** — new knowledge, meta-currency, narrative beat, or unlock.

**Don't**
- [ ] Don't ship a binary easy mode that deletes the core loop, or bury/stigmatize the assist toggle.
- [ ] Don't leave a zero-cost reload path that reverses death or re-rolls outcomes (scum-faucet).
- [ ] Don't use hidden rubber-banding; don't leave flat difficulty that bores masters.
- [ ] Don't return the player to an identical state with nothing gained (pure-reset).

**Test for** — Is median run length bounded? Does the assist ease on repeated failure without disabling stakes? Is there any trivial reload that re-rolls a death/loot/event? Does each death bank ≥1 of {knowledge, currency, narrative, unlock}?

---

## Meta-progression & diegetic death (Area 3)

**Do**
- [ ] State the **roguelike↔roguelite position** explicitly; tag every generated reward run-local or permanent at creation.
- [ ] Make permanent upgrades **widen options / soften variance**, not remove the need to play well.
- [ ] **Canonize death in the fiction** — an in-world reason to return; NPCs/world acknowledge prior deaths; some content gated behind failed runs.
- [ ] Front-load identity-shaping unlocks; reserve later ones for optimization; provide an escalation path past "first win."

**Don't**
- [ ] Don't ship an ambiguous middle persistence state (identity confusion).
- [ ] Don't let power creep out-level skill (grind-to-win), or gate skilled players behind grind walls.
- [ ] Don't leave death as a mechanical reset the story can't explain (death dissonance).

**Test for** — With max meta + median skill, is the hardest content still *losable*? With min meta + high skill, is early content still *winnable*? Is there an in-world reason the protagonist returns, acknowledged by the world?

---

## ⚠️ Single-save + long-form open world (Area 4 — extrapolation, playtest required)

**Do**
- [ ] Make the **character mortal but the world-state persistent** (map, factions, events, economy, finite-loot locations survive).
- [ ] Use a **legacy/succession frame** (heir/reincarnation/cursed undead); state exactly what is inherited vs. lost; lineage extinction = true game-over.
- [ ] Provide a **diegetic continuity justification** grounded in lore and introduced early (one lore engine: token economy / reincarnation / curse).
- [ ] Consider **corpse-recovery / partial persistence** with a real recovery window.
- [ ] Keep a **legendary-loot conservation ledger**; on death, legendaries re-enter the world (corpse/heir/faction), never silently vanish or duplicate.
- [ ] **Default to opt-in** single-save; ship a legacy/heir default and gate true permadeath behind explicit consent.
- [ ] **Instrument retention-after-catastrophic-loss** and wire kill-switches (fall back to legacy default if churn spikes).

**Don't**
- [ ] Don't treat long-form death like a short-run reset (total-wipe despair).
- [ ] Don't make heirs so weak/disconnected it feels like a restart, or so strong death loses all stakes.
- [ ] Don't bolt on a respawn justification after the mechanics ("why am I back?").
- [ ] Don't let any legendary be permanently destroyed without a recovery window.
- [ ] Don't ship mandatory long-form permadeath as default without retention data.
- [ ] Don't relax the Area 1 fairness backbone here — make it **stricter** (one unfair death can cost the whole game).

**Test for** — After avatar death, do world-state diffs persist while only avatar-bound state resets? Does the legendary-conservation audit hold every generation/death cycle? Does telemetry show acceptable retention past the first catastrophic loss — and if not, is the legacy/heir default the shipping default?

---

## The gating discipline (for headless loops)

- [ ] Build the fairness backbone + survivability check, the seeded per-subsystem RNG, the persistence schema, and the loot-conservation ledger **before** generating content.
- [ ] Watch the thresholds: unfair-death rate >~10% → stop scaling; post-loss churn high → demote to opt-in; dominant option appears → rebalance; conservation audit fails → halt loot gen.

**Test for (the key one)** — Before generating any lethal content: does the automated survivability pass exist and pass on current encounters? If not, you are shipping a **random-death generator** — the worst possible outcome for a permadeath game, and unrecoverable at long-form scale.
