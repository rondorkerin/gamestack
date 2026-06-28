# The gate: headless generate → review → repair loop

`procgen-review` is gamestack's quality gate. This file is the contract for running it
as a headless loop so a generation pipeline can self-gate with no human in the seat.

## The verdict contract

`procgen-review` emits its result as the **final fenced ```json block** in its output:

```json
{
  "pass": false,
  "score": 0.62,
  "failures": [
    { "gate": "oatmeal", "detail": "37 of 50 dungeons share the same beat structure" },
    { "gate": "intentionality", "detail": "no 'who built this' answer for 22 locations" }
  ]
}
```

Extraction is deterministic: **take the last fenced ```json block** in the output. The
skill must not emit an OS exit code — a markdown skill cannot. The exit code belongs to
the harness below.

## The harness (copy-paste)

```bash
# Headless generate → review → repair. Bounded retries = 3 (default).
set -euo pipefail
ATTEMPTS=${ATTEMPTS:-3}
for i in $(seq 1 "$ATTEMPTS"); do
  claude -p "Generate the next batch of <content> per ./.gamestack/bible/constraints.md"
  VERDICT=$(claude -p "Run procgen-review on the batch just generated. Output only the verdict." \
            | awk '/```json/{f=1;next}/```/{f=0}f')          # last-fenced-json extraction
  PASS=$(printf '%s' "$VERDICT" | jq -r '.pass')
  if [ "$PASS" = "true" ]; then
    echo "PASS on attempt $i"
    claude -p "Commit the batch and append the decision to ./.gamestack/bible/decisions.md"
    exit 0
  fi
  echo "FAIL attempt $i — repairing from verdict.failures"
  claude -p "Repair the batch using these failures, then we re-review: $VERDICT"
done
echo "Still failing after $ATTEMPTS attempts — halting for human review"
exit 1
```

## Rules

- **Never commit content that has not returned `pass: true`.** This is the one
  non-negotiable rule of a headless gamestack loop (see ETHOS §4).
- The repair step feeds `failures[]` back to generation; it does not regenerate blind.
- After `ATTEMPTS` failures the harness exits non-zero so CI / the caller halts instead
  of committing oatmeal.
- Phase A will wrap this as a `gamestack-gate` binary; until then it is this snippet.
