# HSCOPE — Scope Guard

**When to use:** Claude starts editing configuration, creating files outside the artifact root, "fixing" things the phase did not ask for, or proposing a large redesign during an audit phase.
**Session:** SAME (the session that is going off track)
**Mutation:** NONE
**Placeholders to fill:** none

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HSCOPE
Operation: Scope Guard

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

STOP. Do not run any further tools or make any further changes yet.

Scope check for the harness phase currently running in this session:
1. Quote the scope and rules of the phase prompt I gave you earlier in THIS
   conversation (its "Mode" and "Rules" statements). If the phase prompt is not in
   this conversation, say so and ask me to state the scope.
2. List everything you have done, and everything you were about to do, that falls
   outside that scope.
3. Using read-only status/diff commands, list every file created, modified, moved or
   deleted since this session started, in any repository, configuration location, or
   outside the harness artifact root. Give paths and change types only, not contents.
4. Do NOT revert, undo or clean up anything. I will decide.
5. Propose the smallest set of options (leave as is / revert specific files / continue
   only after approval) with the risk of each.

Then wait for my decision. After it, resume the phase strictly within its stated scope.
```
