# HCHECKPOINT — Pre-Clear / Pre-Compaction Checkpoint

**When to use:** In the middle of a phase, when context is getting large or you must stop (end of day, before clearing or compacting). It saves exactly where you are so a fresh session can resume. For a *finished* phase use HFREEZE instead.
**Session:** SAME (the session holding the knowledge)
**Mutation:** ARTIFACTS ONLY (writes under the artifact root; never repositories or configuration)
**Placeholders to fill:** none. It detects the phase automatically and asks only if it cannot.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HCHECKPOINT
Operation: Pre-Clear / Pre-Compaction Checkpoint

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Target phase: the harness phase in progress in this environment, detected below. It is
NOT finished. Save the exact working state so a new session can resume it without
this conversation and without any external prompt library.

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

RULES: do not continue the analysis, do not start new work. Do not modify any
repository, configuration or history file. Write only under the artifact root. No
secrets or credential values in any artifact.

1. Partial artifact. Make sure the phase's primary artifact exists (create it if
   needed), write everything established so far into it, and mark unfinished sections
   "NOT YET DONE". Replace conversation-dependent wording ("as discussed", "see
   above") with actual content.

2. Checkpoint file: handoff/<NN>-checkpoint.md (NN = detected phase number), containing:
   - Prompt ID and phase name (logical IDs), date, launch directory, Claude Code
     version, OS and shell
   - steps completed, steps remaining, in order
   - partial results and decisions so far, with reasons
   - verified and unverified facts
   - scratch files and scripts, each with its path (relative to the artifact root) and
     the exact command to re-run it
   - the exact point at which to resume and what to do first
   - anything that exists only in this conversation (persist it now or list it as lost)
   - open questions and who must answer each

3. Workflow state. Update the workflow-state file: Current major phase = this phase;
   Status = IN PROGRESS; Last operation = "HCHECKPOINT - saved"; Next operation =
   "resume this phase in a NEW session after HRECOVER"; Next session = NEW; Primary
   artifact; Handoff = the checkpoint file; Blocking items; Last updated. Do not change
   "Last completed major phase".

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

4. Verify. Re-read the files you wrote and list them with sizes.

End with "CHECKPOINT SAVED - safe to clear", then the recommended next operation by
logical ID. Do not mention any external file names.
```
