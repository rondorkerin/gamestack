# Engine overlay: Unity

Loaded when the target engine is Unity. **Coverage is partial.** The only curated pack
today is debugging-focused (third-party, MIT — jahro-console/unity-agent-skills, the
Jahro console). A general Unity-authoring pack is a gamestack roadmap gap.

## Install the engine pack
```
/plugin install unity-jahro@gamestack
```

## Spec → engine-pack skill mapping

| Need | Pack skill(s) |
|------|---------------|
| Structured logging while building | `jahro-logging` |
| Runtime commands / live inspection | `jahro-commands`, `jahro-watcher` |
| State snapshots for repro | `jahro-snapshots` |
| Setup / production / migration | `jahro-setup`, `jahro-production`, `jahro-migration` |
| Troubleshooting a build | `jahro-troubleshooting` |

## The gap (be honest)
There is **no curated general Unity-authoring pack** here yet — nothing covering MonoBehaviour
patterns, ScriptableObject-driven systems, the input system, or addressables. Until one
is slotted in:
- Implement from the gamestack design specs plus general C#/Unity knowledge.
- Drive systems data-first with ScriptableObjects so `constraints.md` stays the source of
  truth (the Unity analog of Godot resources / Unreal data assets).
- Use the `unity-jahro` pack for the debugging/observability layer while you build.

State this gap to the user rather than pretending full coverage exists.

## Handoff note
`engine-router` passes the bible as the engine-independent contract. Translate, do not
redesign. Flag the authoring-pack gap as part of the handoff.
