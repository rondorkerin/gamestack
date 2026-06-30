# Combat Design & Game Feel — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for combat and game feel. Run these against a feel-tuning pass, an enemy/encounter spec, or a batch of generated combat content. The Test-for items are written to be enforced as automated validators in a headless loop. See `GUIDE.md` for reasoning and sources.

---

## Combat juice (applied game feel)

> General feel — input/latency, forgiveness, the full juice toolkit, animation principles, camera/UI — lives in **`game-feel-and-juice`**. These items are the **combat-specific** slice only.

**Do**
- [ ] Apply the **Medium–High feedback budget** to *hits* — a measurable budget per hit event (particle systems, peak shake, hit-stop/sec).
- [ ] Give every combat **outcome** a distinct, non-colliding signature (hit, crit, block, parry, kill, whiff, take-damage).
- [ ] Build each impactful hit from a **bounded bundle**: hit-stop (~0.05s+), shake (amplitude + decay), ≥1 sound layer, hit-reaction — scaled to magnitude, from the per-event table.
- [ ] Reserve **maximal effects** (slow-mo finisher) for rare tagged events (boss kills, legendary loot), capped per session.

**Don't**
- [ ] Don't maximize combat juice — *Extreme* hurts experience, motivation, *and* performance as much as *None* (Kao 2020).
- [ ] Don't make every outcome flash the same (can't tell a parry from a block).
- [ ] Don't let hit-stop stack across rapid hits (stutter / laggy feel); don't ship hits with no stop or reaction (floaty feel).
- [ ] Don't spend peak effects on routine hits (spectacle inflation).

**Test for** — Does any combat scene exceed the High ceiling on summed feedback or drop FPS under peak juice? Can each combat outcome be told apart by feel alone?

---

## Telegraphing & readability

**Do**
- [ ] Give **every attack with damage ≥ lethality threshold** an anticipation phase ≥ a minimum time before the active frame.
- [ ] Make telegraph salience **monotonic with damage** — bigger hits get longer / more multi-channel (anim + VFX + SFX) warnings.
- [ ] Use **one closed, documented danger vocabulary**; map every enemy's attacks onto existing cue types.
- [ ] Keep **player** attack startup ≤ a responsiveness ceiling; convey weight via **follow-through / VFX**, not added startup.
- [ ] Keep the anticipation→hit interval **consistent per attack** and inside a reaction-feasible band.

**Don't**
- [ ] Don't ship instant, unreadable damage (the un-telegraphed kill).
- [ ] Don't give a chip-poke and a one-shot indistinguishable cues (danger/cue mismatch).
- [ ] Don't invent a novel per-enemy symbol, or let a cue overlay fully occlude the animation it warns about.
- [ ] Don't add long player wind-ups (reads as input lag).
- [ ] Don't use feint/delayed timing as a player's **first** exposure to an attack; tag and rate-limit feints.

**Test for (the key one)** — For every attack: anticipation ≥ min for its damage tier, cue ∈ approved vocabulary, interval in reaction band unless flagged a feint. **In ghost-runs, deaths-without-a-preceding-telegraph must be zero.**

---

## Enemy design

**Do**
- [ ] Give each enemy a **unique silhouette** readable by type at mid-range (~10+ m).
- [ ] Compose rosters by **combat role** (grunt/tank/skirmisher/artillery/swarm/leader/controller), tracked in a roster matrix (speed/health/range/DPS).
- [ ] Build enemies from a **shared move library** with per-enemy loadout selection enforcing thematic distinctness.

**Don't**
- [ ] Don't ship recolors/scale-swaps that read identically in a crowd (silhouette soup).
- [ ] Don't fill an encounter with one role (mono-role mobs).
- [ ] Don't reuse both assets *and* loadouts so "new" enemies fight identically (palette-swap fatigue).
- [ ] Don't over-stuff boss movelists past engine AI limits (the Sigrún constraint).

**Test for** — Does the silhouette outline differ above threshold from every other roster member? Do no two enemies share more than X% of their active loadout?

---

## Encounter design

**Do**
- [ ] Put **≥2 distinct roles** in every non-trivial encounter.
- [ ] Give every arena an **inward incentive** *and* ≥1 role that punishes threshold-camping (solve the door problem).
- [ ] Cap **concurrent aggressive attackers** (tunable per difficulty; default low, e.g. 2) via an aggression-token pool.
- [ ] Provide **off-screen attack telegraphs** (e.g. directional arrows: incoming / idle-near / ranged).
- [ ] Pace each encounter as a story: **setup → escalation → climax → release**, with rest beats between spikes.

**Don't**
- [ ] Don't let the optimal play be retreating to funnel enemies at a doorway (doorway cheese).
- [ ] Don't allow the whole pack to attack at once or aggression to transfer instantly to a full swarm (gank death).
- [ ] Don't run constant-intensity attrition with no rhythm (flatline pacing).

**Test for** — Is concurrent-aggressor count ≤ the cap in simulation? Does every arena have an inward pull and a camping-punisher? Does intensity vary over the encounter?

---

## High-lethality / Souls-like loop

**Do**
- [ ] Make attacks **committal** (non-cancelable past a startup window) and gate every verb on **stamina** with a cost + recovery.
- [ ] Ensure **every death has a readable, learnable cause** (telegraph existed; resource mismanaged).
- [ ] Make every enemy **defeatable by ≥2 distinct vetted strategies**; allow respec / alternate paths.
- [ ] Include a **bounded comeback mechanic** (Rally-style: reclaim recent HP by re-engaging within a short window).
- [ ] Keep the **retry loop short**; replenish healing through play; place **checkpoints before** lethal set-pieces.

**Don't**
- [ ] Don't allow instant cancels that remove commitment risk (mash-cancel soup).
- [ ] Don't ship RNG-only, unavoidable, or unreadable deaths (unfair difficulty).
- [ ] Don't let an irreversible early choice brick a run (build-lock regret) — critical under single-save + finite loot.
- [ ] Don't make being hit pure punishment with no re-engage option (turtle meta).
- [ ] Don't impose long runbacks / loss spirals (punitive retry tax).

**Test for** — Empty stamina creates a punishable state? Ghost-runs show zero un-telegraphed deaths? Every enemy beatable ≥2 ways? Checkpoint-before-setpiece invariant holds? Death-to-retry time below ceiling?

---

## Headless guardrails (author once, enforce always)

These are the **hand-authored contracts** the generator must respect — author them before generating, validate every batch against them, and **block commit** on failure:

- [ ] **Feedback table** (event × magnitude → bounded juice bundle), capped to Medium–High → **juice-budget linter**.
- [ ] **Danger-cue vocabulary** + **monotonic damage→telegraph-salience curve** → **telegraph validator**.
- [ ] **Role taxonomy + base move library + base silhouettes** → **silhouette-distinctness + roster-dup checks**.
- [ ] **Aggression cap per difficulty** → **concurrent-aggressor check**.
- [ ] **Stamina / commitment numbers, damage tiers, checkpoint rules** → **fairness ghost-runner** (deaths-without-telegraph → 0).

> Gate every generated batch through `procgen-review` as well — sameness and unfairness are invisible from inside a single sample.
