# Combat Design & Game Feel — Guide

The moment-to-moment discipline of combat: how feedback, telegraphing, enemy design, and lethality combine into fights that feel powerful *and* fair. Pair with `CHECKLIST.md` for the actionable version.

> Drawn from empirical research (Dominic Kao's juiciness studies), the animation-principles lineage (Thomas & Johnston → Cooper, Totten), and the design discourse around *God of War* (2018), FromSoftware (*Dark Souls*, *Bloodborne*, *Sekiro*, *Elden Ring*), *Death's Gambit*, and the readability work at Valve and Nintendo. Sources noted inline.

> **The central law:** *telegraphing is fairness, and juice has a ceiling.* Every lethal attack gets a readable wind-up whose salience scales with its damage; total feedback is tuned to a Medium–High band, never maximized. The biggest effects are reserved for the rarest moments.

> For the general theory underneath this (interesting decisions, flow, difficulty curves, reward schedules, motivation) see `game-design-fundamentals`. This skill specializes it for combat.

---

# Sub-domain 1 — Combat Juice (applied game feel)

> **The general theory lives in `game-feel-and-juice`** — Swink's three building blocks, input latency & forgiveness, the full juice toolkit, the inverted-U feedback ceiling (Kao 2020, N=3,018), the 12 animation principles, and camera/UI feel. Read it first. This sub-domain only covers what is **specific to combat**: applying the feedback budget to *hits*, and giving combat *outcomes* distinct signatures. Don't re-derive the ceiling here.

## 1.1 Apply the inverted-U feedback budget to hits — never maximize

Combat is where juice overload bites hardest: an agent that maximizes shake and particles on every swing produces the "Extreme" condition Kao found *worse* than none. Author a **combat feedback table** keyed by (hit tier × outcome) inside the Medium–High band, and reserve the maximal tier (slow-mo finisher — *Kid Icarus: Uprising*, *Devil May Cry*) for tagged rare events (boss kills, finite-legendary-loot acquisition), capped per session.

- **Test for:** a measurable budget per hit event (particle systems, peak shake, cumulative hit-stop/sec); flag any combat scene over the High ceiling; maximal-tier effects fire only on tagged rare events.
- **Failure modes — Juice overload** (sensory noise tanks readability + FPS) and **spectacle inflation** (peak effects on routine hits leave nothing for climaxes).

## 1.2 Give every combat outcome a distinct, non-colliding signature

Loud is worthless if the player can't read *what happened* (Kao CHI 2024: amplification backfires when it impairs agency). The combat-specific requirement is that the outcomes the player must distinguish — **hit, crit, block, parry, kill, whiff, take-damage** — each read differently.

- **Test for:** hit, crit, block, parry, kill, whiff, and take-damage each have a distinct feedback signature that doesn't collide.
- **Failure mode — Undifferentiated feedback:** everything flashes the same; the player can't tell success from failure (or a parry from a block).

## 1.3 Scale the hit bundle with magnitude; hit-stop is the load-bearing primitive

A combat impact is an assembled, time-bounded bundle: anticipation/hit-frame/follow-through + shake + VFX + hit-reaction/knockback + layered sound + **hit-stop**. Hit-stop (~0.05 s+, scaled to damage) is the single highest-impact combat primitive (*Dark Souls II* is the cautionary weak-feel case); cap cumulative hit-stop so rapid combos don't feel laggy. (Mechanics of all these primitives → `game-feel-and-juice` §C.)

- **Test for:** each impactful hit fires a *bounded* bundle with explicit caps, scaled to magnitude; hit-stop durations come from the per-event table, not live computation.
- **Failure modes — Floaty combat** (no hit-stop/reaction) **or Stutter combat** (hit-stop stacks across rapid hits).

**→ Procedural / headless implication.** The **combat feedback table** is the combat-specific slice of the game-wide feedback table (`game-feel-and-juice`): same Medium–High guardrails, keyed by combat hit tier × outcome. The generator selects and composes from it — never invents combat feedback intensities. The juice-budget linter and FPS-under-peak-juice telemetry are shared with the general skill; here they run over combat scenes specifically.

---

# Sub-domain 2 — Readability & Telegraphing

> The 12 principles of animation (Thomas & Johnston, *The Illusion of Life*) applied to games (Jonathan Cooper, *Game Anim*; Chris Totten). The key inversion: **anticipation is good on enemies (it warns) and bad on the player (it reads as input lag).**

## 2.1 Every potentially lethal attack must have a readable anticipation (wind-up)

Telegraphing is the question the enemy asks the player: *"Here I come — can you avoid this?"* If the player then takes the hit, it's fair, because they were asked a question they could read. *Dark Souls*, *Punch-Out!!*, *Metroid Dread*, and *Cuphead* are the exemplars of clear wind-ups.

- **Test for:** every attack with damage ≥ a lethality threshold has an anticipation phase ≥ a minimum time/frame count before the active hit-frame.
- **Failure mode — The un-telegraphed kill:** instant, unreadable damage; the player dies without having been "asked a question." Catastrophic under single-save.

## 2.2 Telegraph salience must scale with damage ("relative danger = perceived danger")

Bigger payoff earns a bigger, longer, more multi-channel warning. The mapping must be **monotonic**: higher-damage attacks get longer and/or more-channel (anim + VFX + SFX) telegraphs.

- **Test for:** flag any attack whose damage percentile exceeds its telegraph-salience percentile.
- **Failure mode — Danger/cue mismatch:** a chip-damage poke and a one-shot use indistinguishable cues.

## 2.3 Use one consistent, game-wide danger vocabulary

*Sekiro*'s red 危 ("danger") kanji marks unblockable perilous attacks (thrust/sweep/grab), each with a learned counter — learned once, reused everywhere. **But** players note the kanji can *occlude* small enemies and conflates several attack types: a symbol must **augment** body-language readability, never replace it.

- **Test for:** a closed, documented cue set; every generated enemy maps its attacks onto existing cue types; no encounter introduces a novel symbol; cues never fully occlude the animation they warn about.
- **Failure mode — Cue Babel:** per-enemy or inconsistent cue languages the player can't generalize.

## 2.4 Keep the player responsive — minimize player-side anticipation; use follow-through for weight

Anticipation is "only a major issue on player characters" (Cooper/Totten). Minimize player attack startup; convey weight through **follow-through and VFX**, which have fewer gameplay restrictions. *Hollow Knight* lets the swing VFX outlast the recovery so control returns fast while the hit still feels heavy.

- **Test for:** player attack startup ≤ a responsiveness ceiling; weight conveyed via follow-through, not added startup.
- **Failure mode — Sluggish avatar:** long player wind-ups read as input lag.

## 2.5 Beware delayed/feint telegraphs that defeat reaction

A *telegraph* says "dodge NOW." An *excessively delayed* animation (the debated *Elden Ring* case) starts indicating an attack but punishes dodging on the visual cue — forcing rote memorization over reading. *Death's Gambit*'s rule (Jean Canellas): "you got to commit to your attacks and the enemies also commit to theirs."

- **Test for:** the anticipation→hit interval is consistent per attack and inside a reaction-feasible band; feint/delay variants are explicitly tagged, rate-limited, and **never the player's first exposure** to that attack.
- **Failure mode — Knowledge-check combat:** first-sight unreadable timing that only rewards memorization. Acute risk under single-save.

**→ Procedural / headless implication.** The danger-cue vocabulary and the damage→telegraph-salience curve are **inviolable hand-authored contracts**. Generated attacks are assembled from a library of pre-vetted `(anticipation, active, recovery, cue)` animation quadruples. Build a headless validator that, per generated attack, asserts: (a) anticipation ≥ min for its damage tier; (b) cue ∈ approved vocabulary; (c) interval within the reaction band unless explicitly flagged a feint. **This is the single highest-priority guardrail in a lethal game.**

---

# Sub-domain 3 — Enemy & Encounter Design

## 3.1 Give every enemy a unique, readable silhouette

Players should discern enemy *type* by silhouette at mid-range (~10+ m) — Valve's "read hierarchy" (Gamefest 2008); *Halo*'s Grunt/Elite/Jackal/Hunter are canonical. (See `open-world-design` §10 for the same principle applied to landmarks.)

- **Test for:** automated silhouette-distinctness check — black-on-white outline differs above a threshold from every other roster member at simulated mid-range.
- **Failure mode — Silhouette soup:** recolors / scale-swaps that read identically in a crowd.

## 3.2 Compose rosters by combat role, not by skin

Use a role taxonomy (D&D 4e lineage): Grunt, Squad, Leader, Tank, Swarm, Sniper/Artillery, Skirmisher, Controller. Roles *force* tactical variety — "3 Ogres" is flat; one of each role is a fight. Maintain a roster matrix (speed/health/range/DPS) with no accidental duplicates and no permanently-obsolete types.

- **Test for:** each non-trivial encounter contains ≥2 distinct roles; the roster matrix has no accidental duplicates.
- **Failure mode — Mono-role mobs:** three identical brutes; tactically flat.

## 3.3 Solve the "door problem of combat design"

Andrew Yoder (2019): players will fight *from a safe threshold into* an arena instead of entering it — "a bad pattern that reliably produces boring gameplay" — unless lured in. Fixes: reward advancing (resource/objective/flank), and compose enemies (artillery/leader) that *punish* camping. (Distinct from Liz England's "door problem" about role breakdowns.)

- **Test for:** every generated arena has an inward incentive *and* ≥1 role that punishes threshold-camping.
- **Failure mode — Doorway cheese:** optimal play is to retreat and funnel; the authored arena goes unused.

## 3.4 Engineer readable chaos in multi-enemy fights

*God of War* (2018) (Mihir Sheth, GDC 2019) used an **aggression-token pool**: enemies are scored (can-be-aggressive, designer priority, is-targeted, on/off-screen + angle + distance), sorted, and handed tokens from a fixed pool — past the cap, everyone else turns non-aggressive. Practically, only **~2 enemies attack at once** on normal vs a draugr pack. Off-screen threats use circling directional arrows (red = incoming, white = idle-near, purple = ranged), not a minimap; an interrupted attacker briefly *keeps* its tokens so aggression can't instantly swarm. (The "14-token" figure is the *illustrative slide example* — don't hard-code it; tune the pool per difficulty.)

- **Test for:** a tunable cap on concurrent aggressive attackers (default low, e.g. 2); off-screen attack telegraphs exist; aggression can't transfer instantly to a full swarm.
- **Failure mode — Gank death:** many simultaneous attackers, no off-screen warning; the *camera*, not skill, kills the player.

## 3.5 Get distinct fights from shared assets via curated movesets

The nine *God of War* Valkyries (Denny Yeh, PlayStation.Blog 2019) shared a weapon/move pool but each had a defining theme; the Queen, Sigrún, reused nearly the whole combined moveset — "over 25 attacks." Curate a shared move library with per-enemy *loadout* selection enforcing thematic distinctness. (Note: the real engine hit AI-character limits when Sigrún's decision tree grew too large — a hard constraint a generator must respect; see implication below.)

- **Test for:** a shared move/animation library; no two roster members share more than X% of their active loadout.
- **Failure mode — Palette-swap fatigue:** reused assets *and* reused loadouts, so "new" enemies fight identically.

## 3.6 Pace encounters like a story (setup → escalation → climax → release)

An encounter is a combat story with a beginning, middle, and end ("Creating Conflict," GDC Europe 2016). Vary intensity over time; insert rest beats between spikes.

- **Test for:** generated encounter sequences vary enemy count/role-threat over time; rest beats exist between spikes.
- **Failure mode — Flatline pacing:** constant-intensity attrition with no rhythm.

**→ Procedural / headless implication.** Encode roles, the roster matrix, the silhouette test, the aggression cap, the door-problem incentive, and the pacing curve as **generation constraints + automated validators** — not suggestions. The generator draws from a hand-authored role/move library and assembles under a pacing template. **Needs hand-authoring:** base silhouettes, the base move library, the danger-cue art, and bespoke set-piece bosses — and an AI generator must **not naively over-stuff boss movelists** (the Sigrún constraint).

---

# Sub-domain 4 — Souls-like High-Lethality Combat

> The contract of fair lethality: hard fights, readable causes, short retry loops. (For the *single-save / permadeath* consequence model and meta-progression specifically, see `permadeath-and-lethality`.)

## 4.1 Make attacks committal (animation priority) and gate actions on stamina

In *Dark Souls*, attacks and dodges play to completion (animation priority) and every offensive/defensive verb spends from a limited stamina pool — "attacking stops all stamina recovery until the animation finishes." Over-commitment becomes the core punishable mistake; this is what makes the read-and-punish loop exist.

- **Test for:** attacks non-cancelable past a startup window; every verb has a stamina cost + recovery; empty stamina creates a punishable vulnerable state.
- **Failure mode — Mash-cancel soup:** instant cancels remove commitment risk and collapse the read-and-punish loop.

## 4.2 Design death as a teacher; keep difficulty *fair*

Miyazaki: difficulty was never the goal — "what we set out to do was strictly to provide a sense of accomplishment," and "death in video games is a positive experience" when there's something to learn. The contract: **every death is attributable to a readable, learnable cause** (the telegraph existed; a resource was mismanaged). No death source is purely random or unavoidable on first sight.

- **Test for:** ghost-runs flag any death with no preceding telegraph (links to 2.1); no RNG-only death sources.
- **Failure mode — Unfair difficulty:** unreadable, unavoidable, or RNG-punishment deaths that teach nothing. Corrosive under single-save.

## 4.3 Reward experimentation; don't brick builds

Difficulty should push players to "experiment more with character builds and weapon load-outs" (Miyazaki); no class should feel underpowered (*Death's Gambit*). No single build trivializes content and no viable build is locked out; respec or alternate paths keep experimentation from being permanently punished.

- **Test for:** every generated enemy is defeatable by ≥2 distinct vetted strategies; no irreversible early choice can brick a run.
- **Failure mode — Build-lock regret:** irreversible early choices brick a run — catastrophic in a single-save game with finite legendary loot. (Itemization that feeds builds → `rpg-systems`.)

## 4.4 Use comeback/aggression mechanics so lethality doesn't force passivity

*Bloodborne*'s Rally/Regain: damage first appears as recoverable "orange" health, reclaimable by attacking within ~5s of being hit — "the best defense is a solid, strategic offense." It manufactures a re-engage option where an Estus economy would push retreat. Keep it **bounded** (window/amount) so it doesn't erase lethality.

- **Test for:** ≥1 mechanic rewards calculated aggression after taking damage, bounded in window and amount.
- **Failure mode — Turtle meta:** pure punishment for being hit drives passive, low-engagement play.

## 4.5 Make the checkpoint/respawn loop generous and legible

Souls bonfires/lanterns: hard fights, *short* retry loops. *Bloodborne*'s healing economy is replenished through play (drops), so attrition isn't a dead end. Checkpoints precede — never follow — lethal set-pieces.

- **Test for:** time-from-death-to-retry below a ceiling; healing replenishable through play (not grind/paywall); a checkpoint-before-lethal-setpiece invariant.
- **Failure mode — Punitive retry tax:** long runbacks and loss spirals that punish learning. In a single-save game this is the most delicate balance in the whole design.

**→ Procedural / headless implication.** Stamina costs, commitment windows, damage tiers, and checkpoint spacing are **hand-authored numeric contracts** the generator respects — "fairness" is the hardest property to verify automatically. **Needs hand-authoring:** the death-as-teacher tuning of signature bosses and the single-save consequence model. Build headless validators for: every lethal attack telegraphed (4.2 ↔ 2.1); every enemy defeatable ≥2 ways (4.3); checkpoint-before-setpiece (4.5). Run automated **ghost playthroughs** that flag any death with no preceding telegraph as a fairness violation — and **block release** while that count is > 0.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Juice overload | Screen noise, FPS drops, can't read state | Cap to Kao's Medium–High band (1.1); juice linter |
| Undifferentiated feedback | Hit, crit, parry, kill all flash the same | Distinct signature per outcome (1.2) |
| Floaty / stutter combat | Hits don't register / controls feel laggy | Bounded hit-stop + reaction bundle (1.3) |
| Spectacle inflation | Peak effects on routine hits | Rarity-weighted effect ladder (1.4) |
| The un-telegraphed kill | Instant unreadable damage | Min anticipation per damage tier (2.1) |
| Danger/cue mismatch | One-shot and chip-poke look the same | Monotonic damage→salience curve (2.2) |
| Cue Babel | Per-enemy inconsistent cue languages | One closed danger vocabulary (2.3) |
| Sluggish avatar | Long player wind-ups read as lag | Minimize player startup; weight via follow-through (2.4) |
| Knowledge-check combat | First-sight timing is unreadable | Consistent reaction-band intervals; tag feints (2.5) |
| Silhouette soup | Recolors read identically in a crowd | Unique silhouette per enemy (3.1) |
| Mono-role mobs | Three identical brutes | Role-based roster, ≥2 roles/fight (3.2) |
| Doorway cheese | Player funnels enemies at a threshold | Inward incentive + camping-punisher role (3.3) |
| Gank death | Whole pack attacks at once, no off-screen warning | Aggression cap + off-screen arrows (3.4) |
| Palette-swap fatigue | "New" enemies fight identically | Curated per-enemy loadouts (3.5) |
| Flatline pacing | Constant-intensity attrition | Setup→escalation→climax→release (3.6) |
| Mash-cancel soup | Instant cancels, no commitment | Animation priority + stamina (4.1) |
| Unfair difficulty | RNG / unavoidable / unreadable deaths | Death-as-teacher; ghost-run validator (4.2) |
| Build-lock regret | Irreversible early choice bricks a run | ≥2 strategies per enemy; respec (4.3) |
| Turtle meta | Being hit is pure punishment | Bounded comeback mechanic (4.4) |
| Punitive retry tax | Long runbacks, loss spirals | Short retry loop; checkpoint before set-piece (4.5) |

---

## Caveats

- **Empirical generalizability.** Kao 2020/2024 are large and (2024) pre-registered, but use purpose-built action-RPGs and mostly crowdsourced players. The "Medium–High" optimum is a strong prior, not a guaranteed setting — Kao notes casual games may tolerate more juice and performance-focused games may want less. Confirm with your own telemetry.
- **Don't hard-code the 14.** The *God of War* aggression-token "14" is an illustrative slide number; "≈2 aggressive on normal" is the practical rule. Tune the pool per difficulty.
- **Inference flag.** The Valkyrie *shared-rig/skeleton* claim is an inference; only the shared *moveset/weapon* reuse is sourced (Yeh, PlayStation.Blog). The verified *God of War* combat-readability source is **Mihir Sheth (GDC 2019)**.
- **Community-opinion sources.** Some readability claims (*Elden Ring* delayed attacks, *Sekiro* kanji occlusion, *Dark Souls* "punishing overcommitment") rest on practitioner/forum consensus, not peer review — used as illustrations of failure modes, not proven law.
- **AI-authoring risk the literature doesn't cover.** No cited source studied *fully AI-authored* combat. The novel risk is *locally* valid but *globally* unfair combinations — two individually-fair enemies whose telegraphs collide, or a generated boss whose decision tree exceeds engine AI limits (the real Sigrún constraint). The mitigation — headless fairness/readability validators before release — is a recommended safeguard, not a proven solution. Treat it as a hypothesis to confirm with telemetry.

---

*Expand with your own playtest findings and citations. See CONTRIBUTING.md.*
