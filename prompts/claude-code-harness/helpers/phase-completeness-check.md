# Phase Completeness Check

**When to use:** Immediately after a major prompt says it is finished, before you freeze the phase or clear the session. It checks the work against the prompt's own requirements.
**Session:** SAME (the session that ran the phase; it needs the phase prompt in its context)
**Mutation:** NONE

## Copy/Paste Prompt

```text
PHASE: <NN and name of the major prompt you just ran>
AUDIT_DIR: <path outside every repository>

Run a completeness check on the phase we just finished. This is read-only: do not
modify any file, do not continue the phase, do not start the next phase.

1. List the output sections/deliverables that the phase prompt required (use the
   prompt as it appears in this conversation; if it is not here, ask me to paste it).
2. Open the persisted report under AUDIT_DIR and, for every required section, mark:
   PRESENT / THIN / MISSING. THIN means present but too vague for a person with no
   access to this conversation to act on it.
3. Evidence check: pick the 5 highest-impact claims in the report and re-verify each
   against the live environment or files (read-only). Mark VERIFIED / WRONG / UNVERIFIED.
4. Rules check: confirm the phase's own rules were respected. List any file created,
   changed or deleted anywhere other than AUDIT_DIR during this phase (use read-only
   status/diff commands for every repository and configuration location involved).
   Report only paths and change types, never contents.
5. Self-containment check: list every place the report depends on this conversation
   ("as discussed", "see above", terms never defined, paths not recorded, decisions
   without reasons).
6. Secrets check: confirm the report contains no credentials, tokens, keys or
   authentication headers. Do not print any you find; report file and line only.
7. Open questions: confirm they are listed and say who must answer each.

Finish with exactly one verdict line:
COMPLETENESS: COMPLETE
or
COMPLETENESS: INCOMPLETE - <numbered list of what must be fixed>

Then stop and wait for me.
```
