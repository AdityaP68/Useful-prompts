# HCHECK — Phase Completeness Check

**When to use:** After a major phase says it is finished, before you freeze it. It checks the phase's persisted output for completeness using only private artifacts.
**Session:** SAME (or any session in the same environment; it does not need the original conversation)
**Mutation:** ARTIFACTS ONLY (it may update only the workflow-state file)
**Placeholders to fill:** none. It detects the phase automatically and asks only if it cannot.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HCHECK
Operation: Phase Completeness Check

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Target phase: the most recently completed or in-progress harness phase, detected below.
The target phase was previously executed in this environment. You do not need the
original prompt text. Determine its execution state from the persistent harness
artifacts produced in THIS environment, then perform the completeness check.

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

EXPECTED CONTENTS of each phase's primary artifact (judge by substance, not headings):
  H01: environment/version facts; scope map; construct inventory with verdicts;
       always-loaded context per launch location; conflicts, duplication, stale and
       misplaced items; portability findings; verdict counts; open questions;
       hand-off notes for the next phase
  H02: data availability and method; aggregate statistics; workflow clusters; classified
       findings with frequency; observed-but-not-justified list; waste inventory;
       baseline metrics for the benchmark phase; open questions
  H03: principles; content-kind classification; level-by-level specification; proposed
       file tree; precedence/conflict rules; knowledge lifecycle; task-state design;
       migration map with before/after context estimates; deliberately undocumented
       items; open questions
  H04: skill platform facts; evaluation of existing skills; candidates with all required
       fields; overlap matrix; P0/P1/P2 ranking with rejected candidates; test plan;
       open questions
  H05: platform facts; existing agents and observed subagent use; candidates with
       evidence and output contracts; agent-vs-skill-vs-script table; minimal set;
       rejected candidates; open questions
  H06: platform facts; hook inventory and verdicts; hook candidates; MCP inventory and
       verdicts; MCP candidates; always-on overhead before/after; security and
       portability findings; open questions
  H07: executive summary; principles; per-construct specification (WHY, SCOPE, WHEN
       LOADED, CONTENTS, WHAT MUST NOT BE INCLUDED, COST/CONTEXT, OWNER, VERIFICATION);
       file tree; P0/P1/P2 plan with file-level P0 detail; before/after context
       estimates; verification and metrics plan; requirements for a later
       custom-intelligence phase; risks; approval checklist
  H08: files created/modified/moved; effective hierarchy; validation results; conflicts;
       always-loaded context before/after; not-implemented items; rollback
       instructions; follow-ups
  H09: verdict summary; findings ranked CRITICAL/HIGH/MEDIUM/LOW with evidence and
       failure scenario; removals recommended; things to leave alone; probe results;
       open questions
  H10: method and conditions; task definitions; results with spread; construct
       contribution; failures and regressions; remaining bottlenecks; recommendations;
       how to re-run

COMPLETENESS CHECK (read-only except for the workflow-state update at the end; do not
continue the phase, do not start another phase):
1. Execution state: does the primary artifact exist and look finished, partial, or
   empty? Say what evidence shows the phase was actually carried out.
2. For each expected content item of the detected phase: PRESENT / THIN / MISSING.
   THIN means too vague for someone with no access to the earlier conversation to act on.
3. Evidence: pick the 5 highest-impact claims and re-verify each against the live
   environment or files, read-only. Mark VERIFIED / WRONG / UNVERIFIED.
4. Rules: list any file created, changed or deleted outside the artifact root during
   this phase, using read-only status/diff commands for the repositories and
   configuration locations involved (paths and change types only, never contents).
   For H08, changes are expected: compare them with the change manifest in its report.
5. Self-containment: list every place the artifact depends on a conversation ("as
   discussed", "see above", undefined terms, decisions without reasons).
6. Secrets: confirm the artifact contains no credentials, tokens, keys or auth
   headers. Never print any you find; give file and line only.
7. Open questions: confirm they are listed and say who must answer each.

Finish with exactly one verdict line:
COMPLETENESS: COMPLETE - target H<NN>
or
COMPLETENESS: INCOMPLETE - target H<NN> - <numbered list of what must be fixed>

WORKFLOW STATE FILE (operational state only: not knowledge, not a conversation summary)
If a workflow-state file already exists in the artifact root (STATE.md, or an equivalent
such as SESSION_LOG that already records harness phases), update that one; never keep
two. Only if none exists, create STATE.md with exactly these fields:
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
Record only: Last operation = "HCHECK - COMPLETE" or "HCHECK - INCOMPLETE"; Next operation =
HFREEZE if COMPLETE (for H08: HREVIEW, then HFREEZE), otherwise "fix the listed items,
then HCHECK again"; Blocking items = the INCOMPLETE list. Do not set Status to COMPLETED:
HFREEZE does that. If you have to create the file because none exists, set Current major
phase = the detected phase, Status = IN PROGRESS, Last completed major phase = NONE,
Primary artifact = its artifact, Handoff = none yet, and fill Next major phase from the
phase order. Refer to prompts only by logical ID, never by file name.
```
