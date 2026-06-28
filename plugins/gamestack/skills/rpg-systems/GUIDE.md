# RPG Systems — Guide

The triad that makes an RPG feel like growth: **character progression**, the **economy**, and **loot & itemization**. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from the Elder Scrolls lineage (*Morrowind*, *Oblivion*, *Skyrim* — including designer Bruce Nesmith's 2025 retrospective), MMO economy theory (Zachary Booth Simpson's Ultima Online model, *EVE Online*, *Path of Exile*), and the *Diablo III* Loot 2.0 / *Reaper of Souls* redesign (Travis Day, Wyatt Cheng), plus *Borderlands* on procedural breadth. Sources noted inline.

> **The central law:** *build identity comes from opportunity cost, the world must not scale to the player, and the path of least resistance must stay "play the game."* Price advancement; gate difficulty by place, not by player level; reserve the top loot tier for items that change how you play; and never let a market or a flood of drops outpace the kill→loot→upgrade loop.

> For the theory underneath (interesting decisions, reward schedules, dominant-strategy hunting) see `game-design-fundamentals`. This skill specializes it for RPG numbers.

---

# Sub-domain A — Character Progression

## A1 Price advancement; never merely permit it

In a classless / learning-by-doing system, make every point of advancement consume a finite, contested resource (real player time, a capped attribute pool, an exclusive slot) so that maximizing one path measurably costs another. *Morrowind* lets all 27 skills reach 100 — identity is not enforced by hard class locks; it *emerges* from how the player spends limited time.

- **Test for:** there exists ≥1 finite resource that strictly decreases as any skill/attribute increases (raising X provably forecloses or delays Y). If every axis can be maxed with no trade-off → FAIL.
- **Failure mode — "Master of everything" convergence:** given enough time every build becomes the same omni-capable character; build identity collapses.

## A2 Assume the cheapest "use" will be spammed — design the use, not just the skill

For any "use it to raise it" mechanic, find the lowest-effort triggering action and ensure it's gated or non-degenerate. *Morrowind* Acrobatics rose per jump regardless of height; Athletics rose by auto-running into a wall. The model rewarded *frequency*, not *meaningful use*, so the optimum was rhythmic spam.

- **Test for:** for each use-based skill, enumerate the minimal triggering action; if it can repeat with no resource cost, no risk, and no context requirement, flag it and require a cost/cooldown/context gate.
- **Failure mode — Grind-spam degeneracy:** the fastest path to power is the most boring (jump-spam, wall-walking).

## A3 Do not scale the whole world to the player

Reward getting stronger with the ability to overpower previously dangerous content; never raise all enemies *and* loot in lockstep with player level. *Oblivion*'s player-centric scaling produced bandits in glass/Daedric armor and damage-sponge foes; senior designer **Bruce Nesmith** (VideoGamer, 2025): "the world levelling with you was a mistake … that's proven out by the fact it did not happen the same way in Skyrim … a much better way to continue to provide the player challenge without making it feel like 'it doesn't matter that I went up in levels, the dungeon went up with me.'"

- **Test for:** sample encounters/loot across the level range; if the ratio of enemy/loot power to player power is ~constant at all levels (no zones the player trivially dominates, none where overmatched) → FAIL.
- **Failure mode — The Oblivion problem / treadmill leveling:** no felt power gain because the world rises with you; also breaks world logic ("bandits richer than kings").

## A4 Use bounded, location-based difficulty (hand-authored backbone + procedural fill)

Assign each region/dungeon a min/max level band fixed on first entry; let weak areas stay weak and hard areas stay hard, gating the hardest hand-authored content behind high minimums. *Skyrim* encounter zones (Min/Max level, some never reset — Valthume min 24); Todd Howard: "as you level up you are going to see harder things, but the easier things stay around … you'll just decimate it." *Morrowind* does it bluntly — most powerful gear is hand-placed and static, so a skilled low-level player can sneak into Tel Fyr and steal Daedric. (See `open-world-design` for where the zone sits spatially.)

- **Test for:** each zone carries an explicit (min,max) band and reset rule, set at authoring time; some zones have minimums far above start (hard gates), some have low maximums (permanently trivial).
- **Failure mode — Uniform difficulty floor:** every zone normalized to the player, removing both safe power-fantasy areas and aspirational "come back later" zones. (Watch the **first-visit lock** side effect — a zone entered too early can stay permanently trivial.)

## A5 Make convergence expensive in time even where it's possible

If eventual mastery of everything is allowed, make each additional mastery's marginal cost rise steeply so practical builds stay specialized for realistic session lengths. *Morrowind* tolerates eventual maxing but efficient leveling (planning ×5 attribute multipliers) is so fiddly that ordinary play yields specialized, imperfect characters; *Skyrim* removed the eight-attribute system entirely (Howard: Oblivion players would "play for three hours, then think 'I chose wrong'").

- **Test for:** estimate play-time to max all skills; if it's within a normal completion playthrough, raise per-point costs or add specialization incentives until full convergence requires post-completion grinding.
- **Failure mode — Accidental optimization trap:** a hidden "correct" way to level (Oblivion's +5 min-maxing) that punishes natural play with a weaker character.

**→ Procedural / headless implication.** Skill-use systems are *dangerous for an AI author* — they invite optimization the generator didn't anticipate, and a self-tuning agent can rediscover jump-spam degeneracies at scale. **Hand-author the progression backbone** (the spent resource, attribute caps, zone level-bands). Level-scaling must be a *declared, inspectable property per zone*, never an emergent global rule, so A3/A4 can be tested mechanically.

---

# Sub-domain B — Economy

## B1 Model the economy explicitly as faucets, transforms, and drains

Represent every currency/resource as a graph of sources (faucets), transformations, and sinks (drains); balance is the *relative rate* of creation vs. destruction, monitored not assumed. Zachary Booth Simpson's *In-Game Economics of Ultima Online* is the canonical frame — UO created items as monster booty and destroyed them via wear and garbage-collection; without adequate sinks, players hoarded en masse (one stored "over ten thousand identical shirts").

- **Test for:** each resource names ≥1 faucet and ≥1 drain, and exposes an estimated per-hour creation and destruction rate. Drains absent or unquantified → FAIL.
- **Failure mode — Faucet-only economy:** currency/items flow in but never out → inflation (or, in item terms, hoarding and clutter).

## B2 Build sinks players choose, not taxes they tolerate

Prefer sinks tied to desire (status, power, expression, risk) over passive taxes; a sink should feel like an opportunity, with taxes only as a gentle background regulator. *EVE Online*'s defining drain is **destruction** — ships players willingly risk and lose (hundreds of trillions of ISK/year). The cautionary contrast is CCP's "Age of Scarcity": when players felt too poor to undock, concurrent logins and sink activity both fell — punitive scarcity suppressed the very risk-taking that drains the economy.

- **Test for:** classify each sink as desire-driven or tax. If >~half of intended currency destruction comes from punitive taxes rather than desirable purchases/risks, flag for rebalancing.
- **Failure mode — Resented-tax economy:** sinks that feel like friction (repair bills, entry fees) with no aspirational spend; players disengage rather than spend.

## B3 Under scarcity, engineer reasons and safety to spend (defeat hoarding)

In a finite/scarcity economy, actively counter the "too valuable to use" instinct. The **"Elixir problem"** — players hoard their best consumable and finish with it unused. Fixes: tier consumables so only the very top feels precious while mid-tier is freely used (BioShock/Dishonored); slot-based capacity (Zelda) so refills feel stable; ensure bosses are beatable *without* consumables so spending is never punished. Wyatt Cheng's item-side version: avoid environments where players "hold on to a Legendary forever in hopes it gets buffed."

- **Test for:** for each "best" finite item/consumable, check for (a) a replenishment path, (b) an in-fiction prompt to use it, (c) no penalty/regret for using it. If a top item has none, predict hoarding and flag.
- **Failure mode — Hoarding lock / dead inventory:** the most powerful items are never used; the reward never actually pays out — the player "wins" by accumulating, not playing.

## B4 Consider fusing currency and consumption (currency-as-material)

Where you want spending to be intrinsic to progression, make the currency *be* the crafting/upgrade material, so holding wealth and gaining power are the same decision. *Path of Exile* has no gold; orbs are simultaneously trade currency and the reagents consumed to craft — every "purchase" is also a sink. (GGG also dropped currency "less frequently but in larger stacks" for better drop-feel.)

- **Test for:** if there's a single dominant currency that's only a medium of exchange, evaluate converting it to a consumable crafting input; document the decision.
- **Failure mode — Inert hoard currency:** a currency with no consumption use accumulates without bound once a player has "enough," ending progression interest.

## B5 Never let a real-money or frictionless market replace the reward loop

Players must not be able to buy optimal outcomes more efficiently than playing yields them. *Diablo III*'s vanilla Real-Money Auction House: director Jay Wilson (GDC 2013) said the auction houses "really hurt the game" — money became "a much higher motivator than … to simply kill Diablo," and gold's AH "does much more damage" than the real-money one. The fix (Loot 2.0 + *Reaper of Souls*) removed the AH and refocused players "onto farming monsters" (Travis Day).

- **Test for:** compare expected time-to-upgrade via direct play vs. any market/trade/purchase path; if the market path dominates for the median player → FAIL.
- **Failure mode — Auction-house short-circuit:** an external market makes the core kill→loot→upgrade loop economically irrational.

**→ Procedural / headless implication.** Instrument the economy with EVE-style telemetry from day one — per-resource faucet/drain rates the agent reads and rebalances against thresholds (B1). The acute procedural risk is **uncontrolled faucets**: a generator that spawns loot/currency without a matching scaled sink inflates or triggers hoarding faster than a hand-tuned economy. For a finite/diegetic token economy, treat tokens as Path-of-Exile-style currency-materials (B4), bias toward desirable sinks (B2), and add anti-hoarding affordances (B3) — a *finite* economy is structurally biased toward **deflation/hoarding**, the opposite of the inflation classic MMO literature assumes.

---

# Sub-domain C — Loot & Itemization

## C1 Use a rarity ladder with a stable baseline tier

Define discrete rarity tiers and keep a deliberate baseline (common) tier so higher tiers feel increasingly special; don't delete the bottom tier to "improve" loot. Wyatt Cheng: white items are the baseline — "if we removed white items completely, there might be a tendency to feel like blues are the new baseline." Removing the worst tier just relocates the baseline upward.

- **Test for:** the schema includes a common baseline that stays a meaningful fraction of drops; if the lowest tier is effectively absent at endgame, check the next tier hasn't silently become trivial filler.
- **Failure mode — Rarity inflation / baseline creep:** every drop is "epic," so nothing is.

## C2 Make top-tier items change how you play, not just your numbers

Reserve the strongest tier for items that alter mechanics, enable builds, or change skill behavior — not for bigger stat values. *Diablo III* Loot 2.0 / *Reaper of Souls* legendary powers changed skills (cast two Hydras at once; tornadoes trailing a sprinting Barbarian), plus **Smart Loot** (drops biased to the finding character's class/main-stat). The redesign "replaced quantity with quality."

- **Test for:** for each top-tier item, check whether removing it would change *which abilities/build* a player uses (not just damage numbers). If every legendary is a pure stat increase → FAIL.
- **Failure mode — Stat-stick itemization:** "legendaries" that are just bigger +numbers (the central failure of vanilla Diablo III).

## C3 Treat items as permanent; avoid retroactive churn

Once an item is in players' hands, avoid changing it retroactively except for clear health-of-game emergencies, so players use, salvage, and discard without regret. Cheng: "items should have a sense of permanence … avoid creating an environment where players feel they need to hold on to a Legendary forever in hopes it gets buffed." Blizzard reserved retroactive changes for a few documented emergencies (e.g., the Furnace/Rimeheart powers that broke Greater Rifts).

- **Test for:** any system that re-tunes existing items logs a justification against a small enumerated allow-list; ad-hoc retroactive buffs/nerfs are flagged.
- **Failure mode — Patch-anxiety hoarding:** frequent rebalances train players to stockpile rather than use — the Elixir problem in the loot domain.

## C4 Procedurally generate breadth; hand-author identity

Use procedural generation for the high-volume common/magic/rare tiers; hand-author every item meant to be remembered, named, lore-bearing, or build-defining. *Borderlands* proves procedural breadth (Gearbox: "more than 30 billion guns" in BL4) — but Gearbox itself concedes past a threshold "it doesn't matter how big the number is." Identity is hand-made: *Morrowind* artifacts (Umbra) have unique IDs, lore, and fixed placement; *Dark Souls* boss-soul weapons are one-per-soul via Soul Transposition (exclusive choices); *Zelda* key items occupy fixed slots and gate progression.

- **Test for:** every "legendary/unique" has a unique name, authored lore, a deliberate acquisition (hand-placed location or specific boss/quest), and a mechanical identity (C2). A procedurally-rolled "unique" → FAIL.
- **Failure mode — Procedural uniqueness oxymoron:** "legendaries" generated from templates — statistically unique, experientially interchangeable.

## C5 "More loot" is not better loot — respect the variable-reward critique

Tune drop frequency for meaning, not maximal engagement. High-frequency random drops exploit variable-ratio reinforcement (the slot-machine mechanism — dopamine responds to *unpredictability*), which is powerful but erodes the value of any single drop and carries ethical risk (loot-box/gambling research). Loot 2.0's "less is more" — far fewer items, each more likely to matter — was the direct reaction.

- **Test for:** estimate the fraction of drops that are plausible upgrades or build pieces for the finding character; if the overwhelming majority are vendor trash, the generator is manufacturing compulsion, not reward — cut volume and strengthen Smart-Loot biasing.
- **Failure mode — Slot-machine loot:** drowning players in drops to maximize click-through, at the cost of meaning.

## C6 Finite legendary loot demands an authored placement and economy plan

For a fixed pool of legendaries, pre-plan each one's identity, world placement, and acquisition gate, and ensure surrounding economy/scaling doesn't trivialize or strand it. *Morrowind*'s static artifacts work *because* the world is largely non-scaling (A3/A4); *Oblivion*'s scaling *broke* hand-authored reward logic (everyone wears the rare gear); *Dark Souls*' one-soul-one-weapon gives each acquisition weight.

- **Test for:** each finite legendary has a single authored source, power balanced against the *static* zone where it's found (not player level), and no procedural drop can duplicate or supersede it. If legendaries scale with the player or can be farmed → FAIL.
- **Failure mode — Devalued uniques:** hand-authored legendaries undermined by level-scaling (Oblivion) or by procedural items that outclass them.

**→ Procedural / headless implication.** The division of labor is the principle: **the generator owns the rarity ladder's lower tiers and stat ranges; the hand-authored backbone owns every named, lore-bearing, build-defining unique.** Treat the finite legendary list as a *fixed authored manifest* with per-item placement/lore/mechanical-identity fields the generator cannot expand. The biggest AI-specific risk is C4's oxymoron — a generator will happily mass-produce "uniques," so make uniqueness a hard-capped curated set, plus a Smart-Loot relevance filter (C5) and a logged permanence policy (C3).

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| "Master of everything" convergence | Every build ends up identical & omni-capable | Price advancement with finite resources (A1) |
| Grind-spam degeneracy | Fastest leveling is the most boring action | Gate the minimal triggering action (A2) |
| The Oblivion problem | Bandits in Daedric armor; leveling feels pointless | Don't scale the world to the player (A3) |
| Uniform difficulty floor | No safe zones, no aspirational zones | Bounded, location-based level bands (A4) |
| Accidental optimization trap | A hidden "correct" leveling path | Make convergence steeply expensive (A5) |
| Faucet-only economy | Currency/items flow in, never out → inflation | Name ≥1 drain per resource; monitor rates (B1) |
| Resented-tax economy | Sinks feel like friction; players disengage | Desire-driven sinks over taxes (B2) |
| Hoarding lock / dead inventory | Best items never used | Replenishment + use-prompt + no use-penalty (B3) |
| Inert hoard currency | Currency piles up with nothing to spend on | Currency-as-crafting-material (B4) |
| Auction-house short-circuit | Buying beats playing | Keep play the path of least resistance (B5) |
| Rarity inflation / baseline creep | Every drop is "epic" | Keep a stable common baseline (C1) |
| Stat-stick itemization | Legendaries are just bigger +numbers | Top tier changes how you play (C2) |
| Patch-anxiety hoarding | Players stockpile, fearing rebalances | Item permanence; logged-only retro changes (C3) |
| Procedural uniqueness oxymoron | Template-rolled "uniques" | Hand-author identity; procgen breadth only (C4) |
| Slot-machine loot | Drowning in vendor trash | Fewer, relevant drops + Smart Loot (C5) |
| Devalued uniques | Hand-authored legendaries outclassed/trivialized | Static-zone balance; no procedural duplication (C6) |

---

## Caveats

- **Designer-regret quotes are partly recent and secondhand.** Nesmith's "world levelling was a mistake" is a strong 2025 primary source (VideoGamer; note outlets sometimes misspell "Nesmith" as "Naismith"). But no Bethesda designer is on record naming the +5 attribute-multiplier "feel weaker as you level" trap in their own words — that's documented mainly in player/wiki sources.
- **Loot-rarity and "elixir problem" discourse is largely community opinion.** The variable-ratio/dopamine foundation is solid behavioral psych (Skinner; gambling research, e.g. David Zendle), but "more loot is bad" specifically is designer-blog reasoning, not controlled study.
- **EVE figures come via community analysts.** CCP publishes Monthly Economic Reports; the detailed sink/faucet commentary is third-party interpretation and CCP sometimes disputes the MER's own numbers — treat absolute figures as indicative.
- **Simpson's UO essay is ~1999/2000.** The faucet–drain framework is foundational; specific UO mechanics are dated — use the framework, not the particulars.
- **Borderlands' "30 billion guns" is a marketing permutation figure**, not a count of meaningfully distinct weapons — Gearbox concedes the number stops mattering past a threshold.
- **AI-authoring adds risks the source literature predates.** None of these classics assumed an autonomous generator that can rediscover progression exploits at scale, open uncontrolled faucets, or mass-produce "uniques." The core mitigation: uniqueness, scaling bands, and economic balance must be hand-authored, declared, and machine-testable — never emergent.
- **Finite-loot / single-save / diegetic-token designs push toward deflation/hoarding** — the inverse of the inflation most MMO economy literature addresses. Lean on the scarcity/anti-hoarding guidance (B3) more than on classic gold-sink advice.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
