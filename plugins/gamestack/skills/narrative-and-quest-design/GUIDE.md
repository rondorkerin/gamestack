# Narrative & Quest Design — Guide

Quests that react, factions that force choices, and a reactivity substrate that survives procedural generation. Pair with `CHECKLIST.md` for the actionable version.

> **The central law:** every branch, consequence, and faction state reduces to a **testable condition over named facts** in one canonical store. Build that substrate first; layer doctrine on top.

> **Verification status (read this).** This guide's verification pass is now complete: a clean adversarial round re-checked every rule against primary sources. Each rule is tagged:
> - ✅ **Verified** — confirmed against a primary source in adversarial verification.
> - ⚠️ **Sourced, unverified** — a real source was fetched, but verification didn't complete.
> - ❌ **Refuted / do-not-encode** — actively failed verification.
>
> §1–5 are now ✅ (the quest-quality and faction doctrines, §4–5, were promoted from ⚠️ after re-verification, with quote and attribution fixes applied). §6 lists ❌ claims that remain do-not-encode.

---

## 1. Build reactivity on a facts database ✅

**Rule:** Make a single, dynamically-populated **fact store** (key → value) that quests *and* dialogue both read and write; implement every branch as a **Condition** check that routes the quest signal down a True/False path.

**Exemplar:** *The Witcher 3.* The quest team: "a system called **facts database**, which is basically a list of variables that can be modified or checked by conditions from quest or dialogue level… filled dynamically in real-time as the player goes through the game." CD Projekt Red's official REDkit docs confirm the implementation: a **Condition node** "checks whether a specific condition is currently True or False and immediately releases the signal through either the True or False output," and a **FactsDB Change node** (under "Game Systems Control") mutates facts. (Sources: ctrl500 "Witcher 3 quest design — how to not drown"; CDPR REDkit "HOW-TO Use quest nodes." Two of three sources are CDPR's own tooling docs.) *Note: facts hold number values, evaluated as true/false by conditions.*

**Test for:** every quest/dialogue branch reduces to a named condition over the fact store; every fact written is read somewhere; every fact read has a guaranteed initial/default value (no orphan reads → nondeterministic branching).

**Anti-pattern:** *ad-hoc state* — reactivity scattered across per-quest flags with no shared store, so consequences can't cross quest boundaries and can't be audited.

---

## 2. Generate procedural quests from world state, not templates ✅

**Rule:** Generate procedural quests with a **planner over explicit world state** (facts about characters, locations, items) plus character preferences — not fill-in-the-blank templates — and run them through the *same* fact store as hand-authored quests.

**Exemplar:** *CONAN* (Breault, Ouellet & Davies, 2018, arXiv:1808.06217): "uses a planning approach to story generation. The engine takes in a world description represented as a set of facts, including characters, locations, and items, and generates quests according to the state of the world and the preferences of the characters." Because it's the same facts-as-substrate idea as §1, a headless agent can use **one** state representation for both authoring branches and generating quests.

**Test for:** generated quests reference only facts that exist in the store (local NPCs, nearby locations, owned items); a quest that can't ground itself in local facts falls back to the hand-authored backbone for that region.

**Anti-pattern:** *template oatmeal* — quests assembled from "go to [X], kill [Y], return to [Z]" slots with no grounding in world state (see `procedural-generation` on the 10,000-bowls problem). **Refuted caveat:** do NOT assume planner output matches hand-authored quality — the claim that CONAN was validated to reproduce commercial quest structures was refuted (§6). Treat generation as *supplement*, gated by `procgen-review`.

---

## 3. Blur flavor vs. consequence so every choice gets weighed ✅

**Rule:** Deliberately **blur the line between cosmetic ("flavor") choices and consequential ones**, so players can't tell which decisions carry weight — and therefore weigh all of them.

**Exemplar:** *The Witcher 3.* Quest Director Mateusz Tomaszkiewicz: "make the distinction between the two as blurred as possible. … If you do it like this **in our observation**, people tend to pay more attention to all of the choices because people don't know which ones are the important ones and which ones are the cosmetic ones." (Source: Game Developer, "From The Witcher 3 to Cyberpunk: the evolution of CD Projekt's quest design." Note: a designer *observation*, not a measured universal.)

**Test for:** the fact store records a broad set of choices including seemingly minor ones, and quests occasionally pay off minor facts — so there's no learnable "tell" that only big choices set facts.

**Anti-pattern:** *consequence tells* — only obvious decisions set facts, so players learn to ignore everything else. The trade-off: blurring increases tracking/QA surface and the risk of **dangling consequences** (facts written but never honored) — audit for them.

---

## 4. The "no fetch quest" doctrine ✅

> Verified against primary sources. The supporting quotes below are confirmed and correctly attributed; the four-part {twist/choice/character/consequence} taxonomy is this skill's synthesis of the anti-filler principle, not a single verbatim CDPR quote.

**Rule:** Every quest should earn its place with a **twist, a choice, a character, or a consequence** — even a monster hunt has a beginning, middle, and end rather than being an arbitrary task. Supporting claims, each verified to its source:
- A good quest is measured by whether **the player feels they impacted** the characters/events/world, not whether they completed a task: "we designed the quests in a way that you feel… that you somehow impact those characters, and events and the world in general." (press-start interview, Tomaszkiewicz — ✅)
- **Empathy** — making players *feel* rather than reason about event logic — is "almost like a secret sauce for making really good quests." (Game Rant, Paweł Sasko — ✅)
- Construction order: **"play, show, then tell, always in that order"** — interaction first, visual storytelling second, dialogue exposition last. (Game Rant, Sasko — ✅)
- Don't add a quest merely to make another quest make sense; content should arise from ideas that feel natural, not connective padding. (Stevivor, Tomaszkiewicz — ✅)
- The disputed caveat: CDPR itself admits shipping some padded/fetch-like quests ("those fetch quests were there because we stuck with ideas we shouldn't have" — Tomaszkiewicz, a *primary* admission); the repeatable "Witcher template" critique (talk to giver → follow Witcher-senses trail → fight → return) is **secondary critic/forum opinion**, and CDPR's own retrospective rejects the formulaic framing — so treat the doctrine as an aspiration with a genuinely disputed failure case, not a guarantee.

**Test for:** each quest can name at least one of {twist, meaningful choice, memorable character, lasting consequence}; quests with none are flagged as filler.

**Anti-pattern:** *errand filler* — quests that exist only to move an item or pad playtime, breaking character logic (asking a hero to run mundane errands without justification).

**Procgen note:** this is the hardest doctrine to generate. A planner (§2) can guarantee *structure* (a complete arc) but not *meaning* (a twist worth retelling). Route every generated quest through the intentionality/fanfic gate in `procgen-review` before commit; default generated quests to the hand-authored backbone for anything story-critical.

---

## 5. Procedural quests as supplement; factions that force choices ✅

> Verified against primary sources. One attribution fix applied below: the geography/hold filtering is sourced to UESP, and only the NPC-type + voice-type details to the Nesmith postmortem.

**Radiant / procedural quests are a supplement, not a substitute.**
- *Skyrim*'s Radiant system is a **template generator that randomizes a constrained subset** (location, enemy, reward) from pre-authored pools — it "cannot create large, complicated, or particularly interesting quests, but it can create a near infinite number of quests with a fraction of the time and effort" of a traditional one. (uesp "Skyrim:Radiant.")
- It respects local lore/geography by **filtering targets to a location's occupants or its hold (region)** (uesp "Skyrim:Radiant"), and restricts quest-giving to **appropriate NPC types** (e.g. innkeepers) with per-voice-type dialogue (gamesbeat, Nesmith postmortem).
- Bethesda **deliberately kept Radiant out of the main quest** — generation for side content, hand-authoring for the critical path. Nesmith on applying it to the main quest: "Sounds great, would play horribly." (gamesbeat, Nesmith postmortem.)

**Faction design: members are not monolithic.**
- *Fallout: New Vegas* (Josh Sawyer): factions feel real because their **members disagree and challenge each other** — "you have to wrestle with the fact that it contains multitudes." A monolithic faction is bad design for two reasons Sawyer names directly: "nobody's like that," and it "paints too simple of a picture for the player and their decision making." (pcgamer, Sawyer, reporting the Examined Game podcast.)
- Factions should have **competing goals and exclusive membership**, so allegiance is a real dilemma. *Morrowind*'s Great Houses make this literal: joining one means "exclusive loyalty to a single house and forsaking ambitions with all other houses." (*New Vegas*; uesp "Morrowind:Factions" — verified, so the earlier "carried by the brief" hedge is dropped.)

**Test for:** every generated/placed radiant quest grounds in local occupants and region; the main/critical path is hand-authored; at least one faction pair has mutually exclusive membership and at least one internal dissenter voiced.

**Anti-pattern:** *radiant substitution* — using generation for story-critical content; *monolith factions* — every member a mouthpiece, so joining is a flavor pick, not a dilemma.

---

## 6. Do NOT encode (refuted) ❌

- **"Quests never fail on player choice; the only failure state is death."** This was **refuted 0-3.** Do not write a hard "quests never fail" rule — choices can and do close off or fail quest lines, and encoding the absolute will produce broken design.
- **"Planning-based generation matches hand-authored quest quality"** (CONAN validated against commercial quests) — **refuted 0-3.** Generation is a supplement; gate it.

General lesson for a literal agent: the source *lore* is full of absolutes ("never," "every," "only"). Re-ground each before executing it literally — the verification round specifically caught an absolute that doesn't hold.

---

## Recommendations (staged)

1. **Facts store first** (✅). One canonical key/value store; every branch a condition over it.
2. **Branch discipline** (✅). Every fact written is read; every fact read has a default. Audit orphans.
3. **Planner over the same store** (✅), gated by `procgen-review` (because quality isn't guaranteed).
4. **Quest-quality gate** (✅). Apply the {twist/choice/character/consequence} test; flag filler.
5. **Factions last** (✅). Exclusive membership + internal dissent.

**Thresholds that change the plan:** (a) a fact read by >1 quest *must* have a defined initial value or branching is nondeterministic; (b) if generated quests can't reference local geography/NPCs, fall back to hand-authoring for that region; (c) if blurring flavor/consequence (§3) creates more than a handful of unhonored facts, add a dangling-consequence audit before increasing choice density.

---

## Anti-pattern quick-reference

| Anti-pattern | Looks like | Fix |
|---|---|---|
| Ad-hoc state | Per-quest flags, no shared store | One canonical facts database (§1) |
| Orphan facts | Facts written-but-never-read, or read with no default | Branch-discipline audit (§1) |
| Template oatmeal | "Go X, kill Y, return Z" with no grounding | Planner over world state + `procgen-review` (§2) |
| Consequence tells | Only big choices set facts | Blur flavor vs. consequence (§3) |
| Dangling consequence | Fact set, never honored | Dangling-consequence audit (§3) |
| Errand filler | Quests with no twist/choice/character/consequence | No-fetch quality gate (§4) |
| Radiant substitution | Generated story-critical content | Hand-author the critical path (§5) |
| Monolith factions | Every member a mouthpiece | Exclusive membership + internal dissent (§5) |
| Hard "no-fail" rule | "Quests can never fail" | Don't encode — refuted (§6) |

---

*§1–5 are verification-confirmed against primary sources; §6 lists refuted claims that remain do-not-encode. Open questions worth a future pass: primary evidence for the BG3 "yes-and" philosophy and the Flamethrower Principle (not yet sourced). See CONTRIBUTING.md.*
