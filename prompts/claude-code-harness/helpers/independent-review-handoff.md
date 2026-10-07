# HREVIEW — Independent Review Handoff

**When to use:** After H08 (implementation) and its HCHECK, to prepare a neutral brief for H09 (adversarial review), which must run in a different, fresh session.
**Session:** SAME (the implementation session, which knows what changed). The review itself runs in a NEW session.
**Mutation:** ARTIFACTS ONLY (writes under the artifact root)
**Placeholders to fill:** none. It detects the implementation phase automatically and asks only if it cannot.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HREVIEW
Operation: Independent Review Handoff

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

Target phase: H08 - P0 Implementation (detected below). Prepare a neutral brief so a
different session, with no memory of this one and no access to any external prompt
library, can independently review the implemented harness. Do not modify any repository
or configuration. Write only under the artifact root.

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
If the detected phase is not H08, stop and tell me.

Write handoff/09-review-brief.md containing FACTS ONLY:
1. Scope: exactly which files and constructs were created, changed or moved, by path
   relative to placeholders, with the scope (user/workspace/repository) and whether
   each is shared or personal.
2. Intended behaviour, written as testable assertions (for example "a session started
   in <REPOSITORY> loads only X", "skill S triggers for requests like Y and not for Z",
   "hook H runs on event E in under N seconds").
3. Where the design, my approval decisions and the implementation report are, as
   paths relative to the artifact root.
4. Baseline and after measurements of always-loaded context, labelled measured or
   estimated, with the method.
5. How to run the validation probes again (commands or steps), and how to roll back.
6. Known deviations from the design, stated factually, and anything not implemented.

Do NOT include: your opinion of the quality of the work, arguments for why choices are
good, or requests that the reviewer go easy on anything. The reviewer must be free to
disagree with every decision.

Then re-read the brief to verify it, and update the workflow-state file's Last
operation to "HREVIEW - review brief written" and Next operation to "HFREEZE, then H09
in a NEW independent session" (update the existing state file; create nothing new if
none exists, and tell me).

End with the exact text I should paste into a NEW session, after H09:
"Read handoff/09-review-brief.md in the harness artifact root first. Treat every claim
in it and in the implementation report as unverified."
```
