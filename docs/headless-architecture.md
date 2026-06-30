# Headless, testable game architecture — a worked example

This is the concrete reference implementation behind two abstract patterns in
[`iteration-loop`](../plugins/gamestack/skills/iteration-loop): the **preview-harness** pattern
(`LOOP.md` §2 — booting one system in isolation) and the **AI playtester** (`LOOP.md` §4 — an
agent that self-verifies via logs/screenshots through a "playtester API"). Those sections
describe the pattern engine-agnostically; this doc shows what it actually looks like as code,
in Godot, with a real CLI invocation. The same shape — input never does logic, every action is
a verb on a seam, every system has a headless preview harness — works in Unity, Unreal, or a
web engine; only the binding layer (Godot's `_unhandled_input`, Unity's `InputAction`, etc.)
changes.

**Why this exists as a separate doc, not a `SKILL.md`:** gamestack's knowledge skills stay
design-spec level, no engine code (see `docs/architecture.md` § "Technical craft is
design-spec, not implementation"). This is different in kind — it's a software-architecture
pattern for testability, not a design discipline — so it lives here as a worked example with
real code, clearly separated from the citation-backed skill content.

---

## The problem this solves

Testing a game system by booting the full game, loading a save, and walking to where the thing
is has a brutal feedback loop, and it isn't reproducible — minor variance in player position,
timing, or RNG state means "go check if the cave generates right" gives you a different answer
each time. Two changes fix this:

1. **Every system can boot in isolation**, headless, and produce the same output for the same
   input, every time (a preview harness / "bench").
2. **Input never performs logic directly** — it routes to a named action that lives in exactly
   one place, so keyboard, gamepad, and an automated test all drive the game through the same
   code path.

---

## Part 1 — Input → Verb → Seam

> **The rule:** an input read never performs game logic. It translates to a verb call on a
> seam, and the seam is the only place the action lives.

A **verb** is a named action (`attack`, `interact`, `dodge`). A **seam** is the one object that
owns the verb's real implementation — everything else (keyboard, gamepad, a test script) just
calls it.

```gdscript
# player_actions.gd — the seam. The ONLY place "attack" is implemented.
class_name PlayerActions
extends Node

signal attacked(target: Node)

func attack() -> void:
    var target := _find_target_in_range()
    if target == null:
        return
    _apply_damage(target, _current_weapon_damage())
    attacked.emit(target)

func _find_target_in_range() -> Node: ...
func _apply_damage(target: Node, amount: int) -> void: ...
func _current_weapon_damage() -> int: ...
```

```gdscript
# input_router.gd — does ONLY translation. No game logic lives here.
extends Node

@export var actions: PlayerActions

func _unhandled_input(event: InputEvent) -> void:
    if event.is_action_pressed("attack"):       # keyboard AND gamepad map to the same action
        actions.attack()
    elif event.is_action_pressed("interact"):
        actions.interact()
```

```gdscript
# test_combat.gd — an automated test. Same code path, no window, no input event at all.
func test_attack_damages_nearest_enemy() -> void:
    var actions := PlayerActions.new()
    var enemy := _spawn_enemy(position = Vector2(10, 0))
    actions.attack()
    assert(enemy.health < enemy.max_health)
```

Project Settings → Input Map binds `"attack"` to both a keyboard key and a gamepad button —
Godot's InputMap already gives you that translation layer for free. The discipline is just:
**`_unhandled_input` (or any input callback) is never allowed to contain game logic** — it only
ever calls a verb. Once that's true, the test above is exercising the *real* code, not a mock.

---

## Part 2 — Test benches: boot one system, not the whole game

A bench is a scene + script that loads only the slice of the game it's testing, takes its
parameters from the command line, optionally captures screenshots, and quits.

```gdscript
# tools/benches/DungeonBench.gd — attached to DungeonBench.tscn
extends Node

func _ready() -> void:
    var args := _parse_args(OS.get_cmdline_user_args())
    var seed_value: int = args.get("seed", 1234)
    var take_shots: bool = args.has("shots")

    var dungeon := DungeonGenerator.generate(seed_value)
    add_child(dungeon)

    if take_shots:
        await get_tree().process_frame   # let one frame render so the viewport has content
        for angle in [0, 90, 180, 270]:
            _camera_look_from_angle(angle)
            await get_tree().process_frame
            var img := get_viewport().get_texture().get_image()
            img.save_png("user://shots/dungeon_seed%d_%d.png" % [seed_value, angle])

    get_tree().quit()

func _parse_args(raw: PackedStringArray) -> Dictionary:
    var out := {}
    for a in raw:
        var parts := a.lstrip("--").split("=")
        out[parts[0]] = parts[1] if parts.size() > 1 else true
    return out
```

Run it:

```bash
godot --headless --path . res://tools/benches/DungeonBench.tscn -- --shots --seed=1234
```

Same seed in, same PNGs out, every time — golden-image testing for procedural generation, with
nothing but a script and a scene. This is the concrete form of `iteration-loop`'s
**preview-harness pattern** (`LOOP.md` §2): boots one system at a time, captures
deterministically on command, outputs to a stable path so the next run can diff against this
one. Build one bench per system that's worth the maintenance (terrain, dungeons, towns,
biomes); skip it for one-off scenes.

---

## Part 3 — Determinism: why headless time isn't wall-clock time

**This is the answer to "how do you handle delta time when so much depends on it (animation,
movement)?"** — a fair question, because by default Godot's main loop still ticks against real
elapsed time even with `--headless` (no window, but the clock still runs).

The fix is a single flag: **`--fixed-fps <N>`**. With it set, Godot treats every processed
frame as exactly `1/N` seconds of `delta` — regardless of how much real wall-clock time the
frame actually took to compute. A physics step, an animation tick, a movement update all see
the same `delta` they would at a perfect, unvarying N FPS, whether the host machine is fast or
slow, loaded or idle.

```bash
godot --headless --path . res://tests/AcceptanceTest.tscn -- --fixed-fps=60 --seed=42
```

Pair `--fixed-fps` with a fixed RNG seed (as in the bench above) and you get a **fully
deterministic** run: identical `delta` sequence, identical random rolls, identical output,
every time, on any machine. That's what makes "did this break combat" a yes/no a CI job can
answer instead of a question that needs a human to eyeball.

(`--fixed-fps` is also what Godot's own `--write-movie` flag relies on internally for
frame-accurate video capture — it's a first-class, documented engine feature, not a workaround.)

---

## Part 4 — A playtest API: verbs composed into a session

Once verbs exist on seams, a "playtest API" is just a thin script that calls them in sequence
and reads the results back:

```gdscript
# tools/playtest_api.gd
class_name PlaytestAPI
extends Node

var actions: PlayerActions
var world: WorldState

func boot() -> void: ...                       # new game, headless, fixed-fps assumed
func goto(location: String) -> void: ...        # pathfind/teleport to a named location
func fight_nearest() -> Dictionary: ...         # calls actions.attack() until the encounter ends; returns a result log
func loot_nearby() -> Array: ...                # calls actions.interact() on nearby lootable nodes
func rest() -> void: ...                        # calls actions.rest(); may trigger level-up

func acceptance_test() -> void:
    boot()
    goto("character_creation")
    # forge a character...
    goto("first_dungeon_entrance")
    var fight_result := fight_nearest()
    assert(fight_result.outcome == "won")
    loot_nearby()
    rest()
    print("ACCEPTANCE: PASS — %s" % JSON.stringify(fight_result))
    get_tree().quit()
```

This **is** `iteration-loop`'s **AI playtester** (`LOOP.md` §4) made concrete: the printed log
line and any screenshots this script produces are the "playtester API" that section describes
generically — programmatic logs + screenshots (+ input injection, here via the verb calls
themselves) that let an agent read its own play session back and self-verify, no human in the
seat. Escalate to a human playtester for the subjective stuff this can't judge ("does this
fight feel good") — this API only answers objective questions ("did the fight resolve, did the
character level up").

---

## Part 5 — Running it in CI

Headless + `--fixed-fps` means the acceptance test above is just as runnable on a GitHub
Actions runner as on a laptop:

```yaml
# .github/workflows/tests.yml
name: tests
on: [push, pull_request]
jobs:
  bench-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Download Godot (headless export templates not needed for tests)
        run: |
          GODOT_VERSION=4.3-stable   # pin to your project's exact version
          wget -q "https://github.com/godotengine/godot/releases/download/${GODOT_VERSION}/Godot_v${GODOT_VERSION}_linux.x86_64.zip"
          unzip -q "Godot_v${GODOT_VERSION}_linux.x86_64.zip"
          chmod +x "Godot_v${GODOT_VERSION}_linux.x86_64"
          sudo mv "Godot_v${GODOT_VERSION}_linux.x86_64" /usr/local/bin/godot
      - name: Run acceptance test
        run: godot --headless --path . res://tests/AcceptanceTest.tscn -- --fixed-fps=60 --seed=42
```

The script calls `get_tree().quit()` on completion; have it set a non-zero exit via
`get_tree().quit(1)` on assertion failure so CI actually goes red. No third-party action
required — downloading the official binary directly keeps this pinned to an exact, known-good
Godot version.

---

## Honest limitations

- **Headless rendering uses a dummy/software renderer.** GPU-only issues (draw-call limits,
  shader/material problems) are invisible until you run it for real. Benches answer logic
  questions; they don't replace a real visual pass — see `iteration-loop`'s inspection channel
  for that.
- **Benches are extra surface area to maintain.** Worth it for systems you iterate on
  constantly (terrain, dungeons, combat); overkill for a one-off scene.
- **A printed log line beats a screenshot for logic questions.** Reserve screenshots for
  genuinely visual questions; a probe that prints the number you're chasing answers a logic
  question faster and without pixel-peeping.
- **None of this replaces actually playing your game.** It replaces *re-checking the same thing
  by hand, by walking there, every single time* — which is the part that was costing minutes
  per iteration instead of seconds.
