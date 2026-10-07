# HFREEZE — Freeze Current Phase

**When to use:** After HCHECK says COMPLETE. It makes the phase's output self-contained, writes a handoff, and records workflow state so a brand-new session can continue without this conversation or this prompt library.
**Session:** SAME (the session holding the knowledge)
**Mutation:** ARTIFACTS ONLY (writes under the artifact root; never repositories or configuration)
**Placeholders to fill:** none. It detects the phase automatically and asks only if it cannot.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HFREEZE
Operation: Freeze Current Phase

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Target phase: the harness phase that was just completed in this environment,
detected below. Persist everything needed so that a brand-new session with no memory
of this conversation, and no access to any external prompt library, can recover the
workflow position and continue.

PHASE DETECTION (do this first; ask me only if it fails)
1. Find the harness artifact root. If a path for AUDIT_DIR was stated earlier in this
   session, use it. Otherwise look, starting in the current directory and its parents,
   for a folder that contains a workflow-state file (STATE.md, or an equivalent such as
   SESSION_LOG that records harness phases) or the harness artifacts listed below.
   If you find none or several, ask me for the path. Do not guess.
2. If a workflow-state file exists, read it: it names the current phase, its status
   and the next operation.
3. Cross-check it against the artifacts that actually exist, and against the handoff
   folder (handoff/<NN>-handoff.md), using this table of logical phases
   (ID - name - primary artifact, relative to the artifact root):
     H01 - Inventory Existing Constructs - 01-inventory.md
     H02 - Historical Usage Mining - 02-history-findings.md
     H03 - Context Hierarchy Design - 03-hierarchy.md
     H04 - Skill Discovery - 04-skill-candidates.md
     H05 - Subagent Discovery - 05-subagent-candidates.md
     H06 - Hooks and MCP Audit - 06-hooks-and-mcps.md
     H07 - Native Harness Synthesis - 07-harness-design.md (my decisions: 07-approvals.md)
     H08 - P0 Implementation - 08-implementation-report.md
     H09 - Adversarial Review - 09-review.md
     H10 - Harness Benchmark - 10-benchmark/report.md (runs in 10-benchmark/runs/)
   Order: H01 H02 H03 H04 H05 H06 H07, human approval, H10 (baseline round), H08, H09,
   human decides fixes, H10 (harness round).
4. If the state file and the artifacts agree, proceed and state which phase you
   detected. If they disagree or are ambiguous, say what you found and ask me one
   question: "Prompt ID / phase name?"
5. If this message includes a HARNESS CONTEXT block, take its phase as the intended
   target, but still check it against the artifacts.
6. Persistent artifacts in this environment are the only source of workflow state.
   Do not look for or depend on any external prompt library, public file names, or
   old conversation transcripts.

RULES: do not start new analysis. Do not modify any repository, configuration or
history file. Write only under the artifact root. No secrets or credential values in
any artifact. Local artifacts may contain real identifiers; remind me they must never
be committed to a shared or public repository.

1. Self-contained artifact. Make sure the phase's primary artifact is complete and
   readable by someone with no access to this conversation: define terms, replace
   "as discussed" and "see above" with the actual content, record real paths relative
   to placeholders, state the method and assumptions, keep VERIFIED and UNVERIFIED
   facts separate with how each was verified, and list open questions with who must
   answer them. Update it in place.

2. Handoff file: handoff/<NN>-handoff.md (NN = the detected phase number) containing:
   - Prompt ID and phase name (logical IDs, never file names), status, date, launch
     directory, Claude Code version, OS and shell, native or compatibility-layer
     environment
   - what was done, in order, in at most 15 lines
   - key decisions and the reasons for them
   - verified and unverified facts, pointing to the artifact sections
   - artifacts: every file written, its path (relative to the artifact root) and purpose
   - rules that were in force (read-only, no secrets, scope limits)
   - important unresolved items and who must answer each
   - what must NOT be redone, and anything that exists only in this conversation (persist
     it now, or list it as lost)
   - the next major phase and the recommended next operation, by logical ID
   - what I should say at the start of the next session (usually nothing)

3. Workflow state. Update the workflow-state file (see the contract below) so that it
   records: Current major phase = this phase (ID and name); Last completed major
   phase = this phase; Status = COMPLETED; Last operation = "HFREEZE - phase frozen";
   Primary artifact; Handoff; Blocking items = the important unresolved items; Next
   major phase; Next operation; Next session; Last updated. Never set VALIDATED:
   only HRECOVER does that.

   Next operation rules:
     H01-H06: if this conversation is large (past roughly half of the context window,
              already compacted, or you are unsure), or the phase is H01 or H06:
              Next operation = HRECOVER, Next session = NEW. Otherwise: Next operation =
              the next major phase, Next session = SAME.
     H07:     Next operation = HUMAN APPROVAL (I will record my decisions in
              07-approvals.md). Next major phase = H10 baseline round, then H08.
              Next session = NEW.
     H08:     Next operation = H09, Next session = NEW and independent. If HREVIEW has
              not produced handoff/09-review-brief.md yet, say so and make HREVIEW the
              next operation instead.
     H09:     Next operation = HUMAN DECISION (which findings to fix; recorded in
              09-decisions.md). Next major phase = H10 harness round, after fixes.
     H10:     Next operation = HUMAN DECISION. Next major phase = NONE.

   WORKFLOW STATE FILE (operational state only: not knowledge, not a conversation summary)
   If a workflow-state file already exists in the artifact root (STATE.md, or an
   equivalent such as SESSION_LOG that already records harness phases), update that
   one; never keep two. Only if none exists, create STATE.md with exactly these fields:
     Current major phase: <ID - name>
     Last completed major phase: <ID - name | NONE>
     Status: <NOT STARTED | IN PROGRESS | COMPLETED | VALIDATED>  (of the current major phase)
     Last operation: <ID - name, result>
     Next operation: <ID - name | HUMAN APPROVAL | HUMAN DECISION>
     Next major phase: <ID - name | NONE>
     Next session: <SAME | NEW>
     Primary artifact: <path relative to the artifact root>
     Handoff: <path relative to the artifact root | none yet>
     Blocking items: <none | short list>
     Last updated: <date>

4. Verify. Re-read every file you wrote, list each with its size, and confirm the
   handoff and state are understandable with no access to this conversation and no
   external library. Refer to prompts only by logical ID, never by file name.

End with "READY TO CLEAR", the files written, and the recommended next operation by
logical ID (for example "HRECOVER - Fresh Session Recovery Test, in a NEW session").
Do not mention any external file names.
```
