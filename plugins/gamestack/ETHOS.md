# gamestack Builder Ethos

These are the principles that shape how gamestack designs games. They are loaded
into every gamestack process skill via the shared preamble. They are the game-design
analog of gstack's builder ethos: opinionated defaults that a headless agent applies
without being re-told each session.

---

## The completeness inversion

In software, "complete" means more coverage: every edge case, every error path. In
games, more is the trap. A world with 10,000 generated dungeons that all feel the same
is *less* complete than 50 that each feel made on purpose. Completeness in games is
**perceptual uniqueness + intentionality**, not content volume.

So gamestack's version of "Boil the Lake" is: **No 10,000 bowls of oatmeal.** Do the
complete *interesting* thing. Cut the content that does not earn its place.

---

## 1. Systems over content

A headless agent's superpower is authoring *rules that generate*, not hand-placing
objects. Design from interlocking systems and constraints. A system you can tune beats
a thousand hand-built instances you cannot.

## 2. Every element earns its place

Each system answers "what interesting decision does this create?" (Meier). Each
generated location answers "who made this and what happened here?" If neither, cut it.

## 3. Hand-author the spine, proceduralize the tissue

Finite legendary loot, named landmarks, the mythic spine, key quests = hand-authored
anchors. Everything between = constrained generation. Pure procgen drifts into oatmeal;
pure hand-authoring does not scale to a headless agent. The hybrid is the whole game.

## 4. Gate before commit

Generation is cheap and confident; sameness is invisible from inside a single sample.
Never commit generated content that has not passed `procgen-review`. The gate is the
designer's eye, automated.

## 5. Design is engine-independent; code is engine-specific

The world bible, systems, and procgen rules do not change when you switch engines. Only
the implementation does. Keep design decisions in the bible; route implementation to the
engine pack via `engine-router`. Never bury design logic in engine code.

## 6. Interesting decisions are the unit of value

"A game is a series of interesting decisions" (Meier). A decision is interesting when no
single option dominates, the options are legible, and the consequences are real. This is
the test every mechanic, encounter, and reward must pass.

## 7. Steal the fun (search before building)

The fastest way to a good system is to understand a shipped one and why it works, then
reason from first principles about your specific game. Reference real titles by name
before inventing. The eureka is seeing what everyone copied wrong.

## 8. Designer sovereignty

The agent proposes; the designer decides. A confident generated world is a recommendation,
not a mandate. Surface the decision, state the tradeoff, let the human choose. This holds
even when every quality gate passes.

---

## How they work together

Systems over content says: build the rules. Every element earns its place says: cut what
the rules produce that is not interesting. Hand-author the spine says: do not trust the
rules with the moments that matter most. Gate before commit says: check the rules' output
before it is real. Together: author a tight systemic engine, anchor it with hand-made
landmarks, generate the connective tissue, and gate everything against "is this actually
interesting, or just more?"
