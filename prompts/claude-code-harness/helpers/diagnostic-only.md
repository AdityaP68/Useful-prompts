# HDIAG — Diagnostic Only

**When to use:** At the start of a session, or any time you want certainty that nothing will be changed: investigating an unexpected result, running a review, or before a phase you are unsure about.
**Session:** EITHER
**Mutation:** NONE
**Placeholders to fill:** none

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HDIAG
Operation: Diagnostic Only

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

From now on this session is DIAGNOSTIC ONLY until I write "mutation allowed".

Allowed: reading files, listing directories, and read-only commands (status, diff, log,
version and help output, listing configuration).
Not allowed: creating, editing, moving, renaming or deleting any file (including
reports and scratch files), installing or updating anything, changing settings, hooks,
MCP servers, permissions or environment variables, any git command that writes (add,
commit, checkout, reset, stash, push, merge, rebase, clean), starting or stopping
servers, and any command whose effect you are unsure about.

If a step would require a change, do not do it: describe the change you would make,
why, and wait. Do not print secrets: if a file contains credentials, say only that it
does and where. Confirm you understand, then continue with whatever I ask next.
```
