# Engine overlay: Unreal Engine

Loaded when the target engine is Unreal. Maps gamestack design specs to the **unreal**
engine pack (third-party, MIT — quodsoler/unreal-engine-skills, 27 C++ skills).

## Install the engine pack
```
/plugin install unreal@gamestack
```

## Spec → engine-pack skill mapping

| Bible / design spec | Unreal pack skill(s) |
|---------------------|----------------------|
| Combat / ability systems (`systems.md`) | `ue-gameplay-abilities`, `ue-actor-component-architecture` |
| Core gameplay loop, rules (`systems.md`) | `ue-gameplay-framework`, `ue-state-trees` |
| Character / movement | `ue-character-movement`, `ue-input-system` |
| World structure, streaming (`world.md`) | `ue-world-level-streaming`, `ue-ai-navigation` |
| Procgen (`constraints.md`) | `ue-procedural-generation`, `ue-data-assets-tables`, `ue-mass-entity` |
| Save / persistence | `ue-serialization-savegames` |
| FX / readability (art spec) | `ue-niagara-effects`, `ue-materials-rendering`, `ue-ui-umg-slate` |
| Multiplayer | `ue-networking-replication` |
| Performance / threading | `ue-async-threading`, `ue-mass-entity` |
| Build / test | `ue-module-build-system`, `ue-testing-debugging`, `ue-cpp-foundations` |

## Conventions & gotchas
- Unreal is C++-first here. Data-driven content (`ue-data-assets-tables`) keeps procgen
  output decoupled from code, matching `constraints.md` as source of truth.
- For large generated populations, prefer `ue-mass-entity` over per-actor spawning.
- Map systemic rules to the `ue-gameplay-framework` (GameMode/State/PlayerState) rather
  than scattering logic across actors.

## Handoff note
`engine-router` passes the bible as the engine-independent contract. Translate, do not
redesign: each `systems.md` entry maps to a pack skill above; design intent is fixed.
