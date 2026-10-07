# HNEXT — What Should I Do Next?

**When to use:** Whenever you come back after a break, lose track of where you are, or a session ended unexpectedly. It answers in **logical IDs only**; you then look the ID up in [PROMPT-CATALOG.md](../PROMPT-CATALOG.md) to find the file to copy.
**Session:** EITHER
**Mutation:** NONE
**Placeholders to fill:** none

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HNEXT
Operation: What Should I Do Next

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Work out where I am in the Claude Code harness workflow and tell me the next step.
Read-only: do not modify anything and do not start any phase.

The workflow (logical IDs; each ID is a prompt I paste from an external library that
you cannot see):
  Analysis phases H01..H07 share one gate:
    run the phase -> HCHECK (completeness check) -> HFREEZE (persist and write state)
    -> if the conversation is large, start a NEW session
    -> HRECOVER (fresh-session recovery test)
         INSUFFICIENT -> HREPAIR -> HRECOVER again in a new session
         SUFFICIENT   -> run the next phase in that same fresh session
  Order: H01 Inventory Existing Constructs, H02 Historical Usage Mining, H03 Context
  Hierarchy Design, H04 Skill Discovery, H05 Subagent Discovery, H06 Hooks and MCP
  Audit, H07 Native Harness Synthesis, then HUMAN APPROVAL of the P0 items, then H10
  Harness Benchmark (baseline round, each run recorded with HBENCH), then H08 P0
  Implementation (fresh session), then HCHECK -> HREVIEW -> HFREEZE, then H09
  Adversarial Review (separate independent session), then HUMAN DECISION on fixes,
  then H10 again (harness round).
  Helpers usable any time: HNEXT (this), HSCOPE (Claude exceeded scope), HDIAG (no
  changes), HCHECKPOINT (session heavy mid-phase), HHISTORY (during H02), HEVAL
  (borderline construct during H04-H06).

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

Then:
1. For each phase, decide NOT STARTED / IN PROGRESS / COMPLETED / VALIDATED
   (VALIDATED = a fresh-session recovery test returned SUFFICIENT), citing which
   artifacts exist, which are missing or look partial. If the state file and the
   artifacts disagree, say so and trust the artifacts.
2. Answer in EXACTLY this format, using logical IDs only:

   CURRENT LOGICAL PHASE:
   <ID - name>

   CURRENT STATUS:
   <NOT STARTED | IN PROGRESS | COMPLETED | VALIDATED>

   RECOMMENDED NEXT OPERATION:
   <ID - name>   (or HUMAN APPROVAL / HUMAN DECISION, with what I must decide)

   NEXT MAJOR PHASE:
   <ID - name | NONE>

   SESSION:
   <SAME | NEW>

   WHY:
   <one to three lines of evidence from the artifacts>

   BLOCKERS:
   <none | missing artifact, unanswered question, missing approval, failed recovery
   test, stale handoff>

3. If something looks wrong (an artifact that is too thin, a phase marked done with no
   handoff), name the corrective operation by logical ID before moving on.
4. Refer to prompts ONLY by logical ID. Never invent, guess or mention file names,
   paths or URLs of prompts: I will look the IDs up myself. Do not write "open" or
   "run the file" instructions.

Keep the whole answer under 25 lines.
```
