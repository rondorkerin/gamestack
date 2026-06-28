# Narrative & Quest Design — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for quests, reactivity, and factions. Run against a quest, a quest generator, or a faction set. See `GUIDE.md` for reasoning and sources. ✅ = verified primary source; ⚠️ = sourced but awaiting re-verification; ❌ = refuted (do not encode).

---

## Facts database (the reactivity substrate) ✅

**Do**
- [ ] Keep **one canonical fact store** (key → value) that quests and dialogue both read/write.
- [ ] Make every branch a **Condition** check that routes True/False over named facts.
- [ ] Give every fact a **defined initial/default value**.

**Don't**
- [ ] Don't scatter reactivity across per-quest flags with no shared store.
- [ ] Don't read a fact that has no guaranteed default (nondeterministic branching).

**Test for** — Is every branch a condition over the store? Is every written fact read somewhere, and every read fact defaulted? (Audit orphan reads/writes.)

---

## Procedural quest generation ✅

**Do**
- [ ] Generate quests with a **planner over explicit world state** (characters, locations, items + preferences).
- [ ] Run generated quests through the **same fact store** as hand-authored ones.
- [ ] Ground every generated quest in **local facts**; fall back to the backbone if it can't.

**Don't**
- [ ] Don't assemble quests from ungrounded "go X / kill Y / return Z" templates.
- [ ] Don't assume generated quests match hand-authored quality (refuted — gate them). ❌

**Test for** — Does each generated quest reference only facts that exist locally? Does it pass `procgen-review` before commit?

---

## Choice & consequence ✅

**Do**
- [ ] **Blur flavor vs. consequence** so players weigh all choices.
- [ ] Record minor choices too, and occasionally **pay off minor facts**.
- [ ] Audit for **dangling consequences** (facts set but never honored).

**Don't**
- [ ] Don't create a learnable "tell" where only big decisions set facts.

**Test for** — Is there any pattern that lets players predict which choices matter? Are there facts written but never read/honored?

---

## Quest quality (no-fetch doctrine) ⚠️

**Do**
- [ ] Give every quest at least one of **{twist, meaningful choice, memorable character, lasting consequence}**.
- [ ] Deliver content **play → show → tell** (interaction first, cutscene last).
- [ ] Aim for the player to *feel they impacted* the world, not just complete a task.

**Don't**
- [ ] Don't add a quest merely to make another quest make sense (connective padding).
- [ ] Don't ship errand filler that breaks character logic.

**Test for** — Can each quest name at least one of the four payoffs? Flag any that can't as filler.

---

## Radiant / supplement quests ⚠️

**Do**
- [ ] Treat generated quests as a **supplement** for side content.
- [ ] **Filter targets to local occupants / region**; restrict quest-givers to appropriate NPC types.
- [ ] **Hand-author the critical path.**

**Don't**
- [ ] Don't use generation for story-critical / main-quest content.

**Test for** — Does every radiant quest ground in local lore/geography? Is the main path hand-authored?

---

## Factions ⚠️

**Do**
- [ ] Give factions **competing goals** and at least one pair with **exclusive membership**.
- [ ] Voice **internal dissent** — members who disagree and challenge each other.

**Don't**
- [ ] Don't make a faction monolithic (every member a mouthpiece) — it oversimplifies the player's decision.

**Test for** — Is at least one allegiance a real dilemma (mutually exclusive, competing goals)? Does at least one faction have a voiced internal dissenter?

---

## Do NOT encode ❌

- [ ] Do **not** write a hard "quests never fail on player choice" rule (refuted 0-3).
- [ ] Do **not** assume planner-generated quests equal hand-authored quality (refuted 0-3).
- [ ] Re-ground any absolute ("never/every/only") from source lore before the agent executes it literally.

**Test for (the key one)** — For every rule the agent will act on literally: is it verified, or is it source lore stated as an absolute? If the latter, soften it or send it back for a fresh verification pass.
