# Guardrails: Stop Scope Creep / Diagnostic Only

Two short interrupt prompts for when a running phase goes off track. Use the first when Claude is doing more than the phase asked for. Use the second when you want a session to look but not touch.

---

## Stop Scope Creep

**When to use:** Claude starts editing configuration, creating files outside `AUDIT_DIR`, "fixing" things the phase did not ask for, or proposing a large redesign in an audit phase.
**Session:** SAME
**Mutation:** NONE

### Copy/Paste Prompt

```text
STOP. Do not run any further tools or make any further changes yet.

Scope check:
1. Quote the scope and rules of the major prompt we are running (its "Rules" and
   "Mode" lines).
2. List everything you have done, and everything you were about to do, that falls
   outside that scope.
3. Using read-only status/diff commands, list every file created, modified, moved or
   deleted since this session started, in any repository, configuration location or
   outside AUDIT_DIR. Give paths and change types only, not contents.
4. Do NOT revert or undo anything. Do NOT clean up. I will decide.
5. Propose the smallest set of options (leave as is / revert specific files /
   continue only after approval) with the risk of each.

Then wait for my decision. After it, resume the phase strictly within its stated scope.
```

---

## Diagnostic Only / No Mutation

**When to use:** At the start of a session, or at any time you want certainty that nothing will be changed (for example while investigating an unexpected result, or before running a phase you are unsure about).
**Session:** EITHER
**Mutation:** NONE

### Copy/Paste Prompt

```text
From now on this session is DIAGNOSTIC ONLY until I write "mutation allowed".

Allowed: reading files, listing directories, and read-only commands (status, diff,
log, version and help output, listing configuration).
Not allowed: creating, editing, moving, renaming or deleting any file (including
reports and scratch files), installing or updating anything, changing settings,
hooks, MCP servers, permissions or environment variables, any git command that
writes (add, commit, checkout, reset, stash, push, merge, rebase, clean), starting
or stopping servers, and any command whose effect you are unsure about.

If a step would require a change, do not do it: describe the change you would make,
why, and wait. Do not print secrets: if a file contains credentials, say only that it
does and where. Confirm you understand, then continue.
```
