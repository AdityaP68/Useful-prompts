# HRECOVER — Fresh Session Recovery Test

**When to use:** In a brand-new session, after HFREEZE. It proves that the private artifacts alone are enough to recover the workflow position. If it passes, this same session is the one in which you run the next major phase.
**Session:** NEW (must have no memory of the previous session)
**Mutation:** ARTIFACTS ONLY (it records its verdict in the workflow-state file and, on failure, writes a gap list; nothing else)
**Placeholders to fill:** none. It detects the phase automatically and asks only if it cannot.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HRECOVER
Operation: Fresh Session Recovery Test

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

You are starting with no memory of earlier work. This is a recovery test: can you
reconstruct the workflow position from the persistent harness artifacts in THIS
environment alone? Do not start the next phase. Do not modify any repository,
configuration or history file.

You must NOT: access any external prompt library, rely on public file names, or open
old conversation transcripts or session history. Using them counts as a failed test.

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

THE TEST. Using only the workflow-state file, the handoff files it points to, and the
artifacts they list (spot-check at most 3 specific claims against the live
environment, read-only, and say which), produce:

RECOVERED STATE
  Last completed logical Prompt ID:
  Phase name:
  Current status:
  Primary private artifact (path relative to the artifact root):
  Unresolved items:
  Next operation (logical ID):
  Next major phase (logical ID and name):

Then:
1. In at most 12 lines, restate what was done, the key findings and the decisions made.
   Do not embellish.
2. State what the next phase will need as input and whether the artifacts supply it.
3. List every question you cannot answer from the artifacts alone. Do not guess or fill
   gaps from general knowledge: write UNKNOWN.
4. State what you would have to re-discover from scratch. Re-discovering work that the
   frozen phase already did counts as a failure.
5. Describe your first three actions for the next phase.

The test passes only if every field of RECOVERED STATE was recovered from the
persistent artifacts, with no UNKNOWN, and nothing needs re-discovering.

Finish with exactly one verdict line:
RECOVERY: SUFFICIENT
or
RECOVERY: INSUFFICIENT - <numbered list of gaps>

THEN RECORD THE RESULT (the only writes allowed):
- Update the workflow-state file's fields: Last operation = "HRECOVER - SUFFICIENT" or
  "HRECOVER - INSUFFICIENT"; Last updated.
  If SUFFICIENT: Status = VALIDATED; Next operation = the next major phase (logical ID);
  Next session = SAME (this session continues into it); Blocking items = none.
  If INSUFFICIENT: leave Status unchanged; Next operation = HREPAIR; Next session =
  NEW afterwards; Blocking items = the gap list, and write the same numbered gap list
  to handoff/<NN>-recovery-gaps.md (NN = the phase number you recovered).
  If no workflow-state file exists at all, that is itself a gap: report INSUFFICIENT
  and do not create one.
- Do not edit any other artifact.

Then stop. If SUFFICIENT, say "Ready for <next major phase ID and name>" and wait for me
to paste it. Refer to prompts only by logical ID, never by file name.
```
