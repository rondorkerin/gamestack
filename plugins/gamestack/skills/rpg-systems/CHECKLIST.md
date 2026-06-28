# RPG Systems — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for the progression / economy / loot triad. Run these against a systems spec or a batch of generated items/economy content. Test-for items are written to be enforced as automated gates in a headless loop. See `GUIDE.md` for reasoning and sources.

---

## Character progression

**Do**
- [ ] Make every advancement **spend a finite, contested resource** (time, capped attribute pool, exclusive slot) so raising X costs Y.
- [ ] For each use-based skill, **gate the minimal triggering action** with a cost, cooldown, or context requirement.
- [ ] Give each zone/dungeon an explicit **(min, max) level band fixed at authoring time**, with a reset rule; include hard-gated high-min zones and permanently-trivial low-max zones.
- [ ] Make full convergence cost **post-completion grinding**, not a normal playthrough.

**Don't**
- [ ] Don't let every axis be maxed with no trade-off (master-of-everything convergence).
- [ ] Don't reward the most boring action with the fastest power (grind-spam).
- [ ] Don't scale all enemies *and* loot in lockstep with player level (the Oblivion problem).
- [ ] Don't normalize every zone to the player (no safe zones, no aspirational zones).
- [ ] Don't ship a hidden "correct" leveling path that punishes natural play.

**Test for** — Does ≥1 finite resource strictly decrease as any skill rises? Does sampling encounters/loot across levels show zones the player dominates *and* zones that overmatch them (ratio not constant)? Is level-scaling a declared per-zone property, never a global rule?

---

## Economy

**Do**
- [ ] Model each resource as **faucets → transforms → drains**, with estimated per-hour creation and destruction rates.
- [ ] Make most destruction **desire-driven sinks** (status, power, risk), with taxes only as a gentle regulator.
- [ ] Under scarcity, give each "best" finite item a **replenishment path, a use-prompt, and no use-penalty** (defeat hoarding).
- [ ] Consider **currency-as-crafting-material** so holding wealth and gaining power are the same decision.

**Don't**
- [ ] Don't ship a faucet-only economy (inflation / hoarding).
- [ ] Don't rely on punitive taxes players merely tolerate (resented-tax economy).
- [ ] Don't let the best items be "too valuable to use" (hoarding lock / dead inventory).
- [ ] Don't leave a dominant currency with no consumption use (inert hoard).
- [ ] Don't let a market/trade path beat playing for the median player (auction-house short-circuit).

**Test for** — Does every resource name ≥1 faucet and ≥1 drain with rates? Is ≤~half of destruction from punitive taxes? Is time-to-upgrade-by-play ≤ time-to-upgrade-by-market?

---

## Loot & itemization

**Do**
- [ ] Keep a **stable common baseline tier** as a meaningful fraction of drops.
- [ ] Make top-tier items **change how you play** (alter mechanics / enable builds), not just numbers.
- [ ] Treat items as **permanent**; log any retroactive change against a small enumerated allow-list.
- [ ] **Procedurally generate breadth** (common→rare); **hand-author identity** (every named/lore/build-defining unique).
- [ ] Bias drops with **Smart Loot** toward the finding character's class/main-stat.

**Don't**
- [ ] Don't make every drop "epic" (rarity inflation / baseline creep).
- [ ] Don't ship stat-stick legendaries (just bigger +numbers).
- [ ] Don't churn items with frequent rebalances (patch-anxiety hoarding).
- [ ] Don't procedurally roll "uniques" (the procedural-uniqueness oxymoron).
- [ ] Don't flood players with vendor trash to maximize click-through (slot-machine loot).

**Test for** — Is the lowest tier still present and meaningful? Would removing each top-tier item change *which build* a player uses? Does every "unique" have a name + lore + authored source + mechanical identity? Is a healthy fraction of drops plausible upgrades for the finder?

---

## Finite-legendary manifest (the running-example case)

**Do**
- [ ] Maintain a **fixed authored manifest** of every legendary: name, lore, mechanical identity, single authored source, and the **static zone level** it's balanced against.
- [ ] Enforce a **hard global cap** on legendary instances across the whole generated world — a constraint, not a probability.

**Don't**
- [ ] Don't let legendaries scale with the player, drop from generic tables, or be farmable/duplicable.

**Test for** — Count of legendary instances across the entire generated world ≤ the authored cap? Each balanced against its static zone, not player level? No procedural drop duplicates or supersedes a manifest item?

---

## Headless guardrails (author once, enforce always)

These are the **hand-authored invariants** the generator must respect — declare them before generating, validate every batch, and **block publish** on failure:

- [ ] **The spent resource + attribute/skill caps** (A1) → convergence-cost check (A5).
- [ ] **Per-zone (min,max) level-band table + reset rules** (A4); level-scaling declared per zone, never global (A3) → encounter/loot-ratio sampler.
- [ ] **The economy graph** (faucets/transforms/drains) with target creation/destruction rates (B1) → faucet/drain telemetry; sink-classification check (B2); anti-hoarding affordance check (B3).
- [ ] **The finite-legendary manifest + global cap** (C6) → uniqueness-cap constraint; procedural-unique detector (C4).
- [ ] **Smart-Loot relevance filter + drop-volume budget** (C5) and **baseline-tier guarantee** (C1); **item-permanence policy** logged (C3).

> Gate every generated batch through `procgen-review` as well — sameness and economic imbalance are invisible from inside a single sample.
