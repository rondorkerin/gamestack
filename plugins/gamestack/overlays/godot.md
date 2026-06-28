# Engine overlay: Godot 4.x

Loaded when the target engine is Godot. Maps gamestack design specs to the **godot**
engine pack (third-party, MIT — jame581/GodotPrompter).

## Install the engine pack
```
/plugin install godot@gamestack
```

## Spec → engine-pack skill mapping

| Bible / design spec | Godot pack skill(s) |
|---------------------|---------------------|
| Combat / ability systems (`systems.md`) | `ability-system`, `component-system`, `csharp-signals` |
| Inventory / economy (`systems.md`) | `inventory-system`, `event-bus` |
| Dialogue / quests (`lore.md`, narrative spec) | `dialogue-system` |
| World structure, navigation (`world.md`) | `ai-navigation`, `3d-essentials` / `2d-essentials` |
| Procgen output → scenes/resources (`constraints.md`) | `assets-pipeline`, `gdscript-patterns`, `gdscript-advanced` |
| Architecture / decoupling | `dependency-injection`, `event-bus`, `component-system` |
| Performance pass | `godot-optimization` |
| Build / ship | `export-pipeline`, `dedicated-server`, `godot-testing` |

## Conventions & gotchas
- Prefer signals + an event bus over hard node references; it matches the systemic
  design ethos (loose coupling = tunable systems).
- Generated content lands as `.tres`/`.tscn` resources, not hardcoded scenes — keep the
  procgen output data-driven so `constraints.md` stays the source of truth.
- GDScript by default; reach for C# (`csharp-godot`) only where the pack recommends it.

## Handoff note
`engine-router` passes the bible as the engine-independent contract. Translate, do not
redesign: each `systems.md` entry maps to a pack skill above; design intent is fixed.
