# Freeze Phase / Pre-Clear Checkpoint

**When to use:** (a) `PHASE-COMPLETE`: after the Completeness Check says COMPLETE, to make the phase's output self-contained and write its handoff. (b) `MID-PHASE-CHECKPOINT`: when context is getting large, before you clear or compact, or before you stop for the day in the middle of a phase.
**Session:** SAME (the session that holds the knowledge)
**Mutation:** ARTIFACTS ONLY (writes under `AUDIT_DIR`; never repositories or configuration)

## Copy/Paste Prompt

```text
MODE: <PHASE-COMPLETE | MID-PHASE-CHECKPOINT>
PHASE: <NN and name of the major prompt>
AUDIT_DIR: <path outside every repository>
NEXT_PROMPT: <file name of the next major prompt, or "same phase, resume">

Persist everything needed so that a brand-new session with no memory of this
conversation can continue. Do not start new analysis. Do not modify any repository,
configuration or history file. Write only under AUDIT_DIR.

1. Self-contained report
   - Make sure the phase report exists under AUDIT_DIR (create or update it in place
     for MID-PHASE-CHECKPOINT: mark unfinished sections "NOT YET DONE").
   - Rewrite anything that depends on this conversation: define terms, replace "as
     discussed/see above" with the actual content, record real paths relative to
     placeholders, state the method used and the assumptions made.
   - Keep VERIFIED and UNVERIFIED facts clearly separate, with how each was verified.

2. Handoff file: AUDIT_DIR/handoff/<NN>-handoff.md containing:
   - phase, mode, status (COMPLETE or PARTIAL), date, launch directory, Claude Code
     version, OS and shell, native or compatibility-layer environment
   - what was done, in order, in at most 15 lines
   - key decisions and the reasons for them
   - verified facts / unverified facts (pointing to the report sections)
   - artifacts: every file written, its path and its purpose
   - rules that were in force (for example "read-only", "no secrets", scope limits)
   - open questions and who must answer each
   - things that must NOT be redone, and things that exist only in this conversation
     and would be lost (persist them now, or list them explicitly as lost)
   - for MID-PHASE-CHECKPOINT: steps completed, steps remaining, partial results,
     scratch files and scripts with the command to re-run them, and the exact point
     at which to resume
   - the exact next step: NEXT_PROMPT, which files it must read, and what I must
     paste into the new session first (placeholders and any approvals)

3. Status: update AUDIT_DIR/STATUS.md (create it if missing with columns
   Phase | Status | Artifacts | Recovery test | Notes). Set this phase to COMPLETED
   (PHASE-COMPLETE) or IN PROGRESS (MID-PHASE-CHECKPOINT). Never set VALIDATED:
   only I do that after a fresh-session recovery test.

4. Safety: no secrets or credential values in any artifact. Local artifacts may
   contain real identifiers, so tell me they must never be committed to a shared
   or public repository.

5. Verify: re-read every file you wrote, list each with its size, and confirm the
   handoff is understandable without this conversation.

End with: "READY TO CLEAR", the list of files, and the exact text I should paste
as the first message of the new session.
```
