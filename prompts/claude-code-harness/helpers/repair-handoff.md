# HREPAIR — Repair Handoff

**When to use:** After HRECOVER returned `RECOVERY: INSUFFICIENT`. It fixes the specific gaps in the private artifacts so the test can be repeated.
**Session:** Preferably the ORIGINAL session if it is still open (it still holds the missing knowledge). Otherwise NEW.
**Mutation:** ARTIFACTS ONLY (writes under the artifact root; read-only inspection elsewhere)
**Placeholders to fill:** none. The gap list is read from the private artifacts; paste it only if Claude cannot find it.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HREPAIR
Operation: Repair Handoff

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Target: the frozen phase whose recovery test failed, detected below. Repair the
persistent artifacts for that phase so a fresh session could recover from them alone.
Do not start the next phase. Do not modify any repository, configuration or history
file. Write only under the artifact root.

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

GAPS: read the numbered gap list recorded by the failed recovery test (the "Blocking
items" field of the workflow-state file and handoff/<NN>-recovery-gaps.md). Only if
you cannot find it, ask me to paste it.

For each numbered gap:
1. Decide where the missing information comes from:
   - this conversation (if it is the original session and still holds it): write it
     down now;
   - the live environment: re-derive it with minimal read-only inspection and label it
     RE-DERIVED with the date and method;
   - nowhere: record it as an explicit open question. Never invent it.
2. Put it in the right place: the phase's primary artifact if it is a finding, the
   handoff if it is process state. Replace conversation-dependent wording with the
   actual content.
3. Do not add unrelated material. Fix only the listed gaps and anything the fix
   directly makes inconsistent.

Then:
- Output a short change log: gap -> where it was fixed -> source (CONVERSATION /
  RE-DERIVED / OPEN QUESTION).
- Re-read the changed files and confirm no secrets were added.
- Update the workflow-state file: Last operation = "HREPAIR - gaps addressed"; Next
  operation = HRECOVER; Next session = NEW; Blocking items = any gap recorded only as
  an open question; Last updated. Do not change Status.

End with: "REPAIRED - run HRECOVER again in a NEW session". Do not mention any external
file names.
```
