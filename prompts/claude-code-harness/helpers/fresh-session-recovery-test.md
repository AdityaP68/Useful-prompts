# Fresh Session Recovery Test

**When to use:** In a brand-new session, after a phase has been frozen, to prove the persisted artifacts are enough to continue without the old conversation. If it passes, this same session is the one in which you run the next major prompt.
**Session:** NEW (must have no memory of the previous session)
**Mutation:** NONE

## Copy/Paste Prompt

```text
AUDIT_DIR: <path outside every repository>
FROZEN_PHASE: <NN of the phase that was frozen>
NEXT_PROMPT: <file name of the next major prompt>

You are starting with no memory of earlier work. This is a recovery test: do not
modify anything, do not start the next phase, and do not rely on anything except
the persisted artifacts.

1. Read AUDIT_DIR/handoff/<FROZEN_PHASE>-handoff.md, then only the artifacts it lists.
   Do not explore the workspace or repositories, except to spot-check at most 3
   specific claims (read-only) and say which ones.
2. In at most 15 lines, restate what was done, the key findings and the decisions
   made. Do not embellish.
3. State what the next phase will need as input and whether the artifacts supply it.
4. List every question you cannot answer from the artifacts alone. Do not guess or
   fill gaps from general knowledge: write UNKNOWN.
5. State what you would have to re-discover from scratch. Any re-discovery of the
   frozen phase's work counts as a failure.
6. Describe your first three actions for the next phase.

Finish with exactly one verdict line:
RECOVERY: SUFFICIENT
or
RECOVERY: INSUFFICIENT - <numbered list of gaps>

Then stop. If SUFFICIENT, say "Ready for <NEXT_PROMPT>" and wait for me to paste it.
```
