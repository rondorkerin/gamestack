# Update procedure

Five steps. Every command below was run live against `gamestack@gamestack` while writing this skill — the output shapes are real, not guessed.

## 1. Find the install

```
claude plugin list --json
```

Find the entry (or entries — gamestack can be installed in more than one project) whose `id` is `gamestack@gamestack`:

```json
{
  "id": "gamestack@gamestack",
  "version": "0.10.1",
  "scope": "project",
  "enabled": true,
  "projectPath": "/Users/you/your-game"
}
```

Note `version` and `scope`.

- If `scope` is `project` and more than one entry matches `id == "gamestack@gamestack"`, prefer the one whose `projectPath` matches the current working directory. If it's still ambiguous, say so and ask which project to update rather than guessing.
- If no entry matches at all, gamestack isn't installed. Report **NEEDS_CONTEXT** and stop, with:
  ```
  /plugin marketplace add rondorkerin/gamestack
  /plugin install gamestack@gamestack
  ```

## 2. Refresh the marketplace catalog

```
claude plugin marketplace update gamestack
```

This pulls the latest `marketplace.json` / `plugin.json` from GitHub. Skipping it means step 3 only sees whatever was cached at install or last update time, so always do this first. Expected output:

```
Updating marketplace: gamestack...Refreshing marketplace cache (timeout: 120s)…
✔ Successfully updated marketplace: gamestack
```

If this fails — git timeout (the docs note a 120s ceiling on all git operations), the marketplace isn't added, or org policy restricts it — report **BLOCKED** with the literal error text. Don't speculate about the cause.

## 3. Update the plugin

```
claude plugin update gamestack@gamestack --scope <scope from step 1>
```

**The `--scope` flag is not optional in practice.** It defaults to `user`, and most installs done via `/plugin install` inside a project are `project`-scoped — omitting `--scope` against a project-scoped install fails with:

```
✘ Failed to update plugin "gamestack@gamestack": Plugin "gamestack" is not installed at scope user
```

Pass the scope from step 1 explicitly. Two real outcomes:

- Already current:
  ```
  Checking for updates for plugin "gamestack@gamestack" at project scope…
  ✔ gamestack is already at the latest version (0.10.1).
  ```
  → Nothing to do. Report **DONE**.

- A newer version existed and was applied → continue to step 4.

- Anything else (network failure, permission error) → report **BLOCKED** with the exact error text.

## 4. Show what changed

If the version moved, fetch the changelog:

```
https://raw.githubusercontent.com/rondorkerin/gamestack/main/CHANGELOG.md
```

Pull out the entries between the old version's `## [X.Y.Z]` heading and the new one, and surface those — not the whole file. If the fetch fails, that's not a blocker for the update itself; note it and fall back to "updated to vX.Y.Z, see https://github.com/rondorkerin/gamestack/blob/main/CHANGELOG.md for details."

## 5. Remind to reload

An updated plugin isn't live in the current session until reloaded. Always end an update (not a no-op check) with: **run `/reload-plugins` to pick it up.** This is the same reminder Claude Code shows after a real auto-update pass — this skill doesn't skip it just because the update was triggered manually.

## Output

One status, per the skill's contract: **DONE** (already current, or updated — state old → new version) / **DONE_WITH_CONCERNS** (updated but the changelog fetch failed, etc.) / **BLOCKED** (the literal CLI error) / **NEEDS_CONTEXT** (not installed, or ambiguous multi-project match).
