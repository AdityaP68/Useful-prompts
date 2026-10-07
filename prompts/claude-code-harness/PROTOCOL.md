# Prompt Protocol: Logical IDs, Self-Identification and Workflow State

This public library and the Claude Code environment where you paste the prompts are **completely separate**. The private environment cannot open files from this repository, does not know what a file name here means, and cannot resolve placeholders from it. This page defines the small protocol that makes manual copy/paste work anyway.

Start with [PROMPT-CATALOG.md](PROMPT-CATALOG.md); this page is the specification behind it.

## 1. Three different things

| | What | Lives in | Used by |
|---|---|---|---|
| **A. Prompt ID** | A stable logical label, for example `H01` or `HCHECK` | Inside every pasted prompt, and in the catalog | Private Claude, to know what it is doing; you, to talk about steps |
| **B. Public GitHub file** | The Markdown file that contains the prompt | This repository | **You only**, to find and copy the prompt |
| **C. Private output artifact** | What Claude writes while executing (reports, handoffs, state) | The private environment's artifact root (`AUDIT_DIR` in the prompts) | Private Claude, in later sessions |

These are never interchangeable.

- The ID is **not** a file reference. Private Claude must never look for a file called "H01" or for the public file.
- The public file exists for the human. Private Claude never needs it.
- The private artifact is how phases communicate. Everything a later prompt needs must be found there.
- The catalog is the only bridge: private Claude says "run HCHECK"; you look up HCHECK in the catalog, open the linked file, copy it, and paste it.

```
PUBLIC GITHUB                          PRIVATE ENVIRONMENT

PROMPT-CATALOG.md
   │  "HCHECK" → file
   ▼
helpers/<file>.md
   │  you copy
   └────────────────────────────►  Claude Code
                                      │  "I am HCHECK"
                                      ▼
                          STATE.md + primary artifacts
                                      │
                                      ▼
                          evaluates the detected phase
```

## 2. ID scheme

- **Major phases:** `H` + two digits: `H01` … `H10`. They mirror the numbered files but are logical names, not paths.
- **Helpers:** `H` + an uppercase word: `HCHECK`, `HFREEZE`, and so on.
- IDs are **never reused and never renumbered**. File names may change freely; only the catalog row changes.
- `H10` appears twice in the workflow (a baseline round and a harness round). It is one prompt used twice.
- In prompt text, "prompt NN" (for example "prompt 02") means phase `HNN`.

## 3. Self-identification headers

Every pasted prompt starts with a short header, so Claude knows what it is without seeing this repository.

**Major prompt header** (inserted at the top of the prompt body; metadata only, it does not change the prompt's instructions):

```text
HARNESS WORKFLOW
Prompt ID: H02
Phase: Historical Usage Mining
Previous Major Phase: H01 — Inventory Existing Constructs
Next Major Phase: H03 — Context Hierarchy Design
Primary artifact: <AUDIT_DIR>/02-history-findings.md

This prompt was manually copied from an external prompt library.
Do not assume that library or its filenames are available in this environment.
The Prompt ID is a logical workflow identifier, not a local file path.
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the
harness artifact root (a folder outside every repository). Earlier phases, if any,
left their results there; inspect those artifacts, not any external files.
When this phase is finished, end your final message with:
"H02 complete. Recommended next operation: HCHECK - Phase Completeness Check."
```

**Helper header:**

```text
HARNESS WORKFLOW
Helper ID: HCHECK
Operation: Phase Completeness Check

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.
```

## 4. Auto-detect first, ask second

You should normally be able to paste a helper with **no edits**. Helpers that act on "the current phase" begin with this block. It is the canonical text; helpers embed it verbatim, because the private environment cannot read this page.

```text
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
```

Do not guess. If detection is not confident, Claude asks one question and stops.

### Optional HARNESS CONTEXT block

You normally do **not** need this. Paste it (before or after a helper) only when Claude says it cannot determine the phase:

```text
HARNESS CONTEXT
Current/target phase: H01 — Inventory Existing Constructs
Operation: Phase Completeness Check

This is metadata only.
The public prompt repository is not available in this environment.
Use persistent local harness artifacts as the source of execution state.
```

Edit the phase and operation to match what you are doing.

## 5. Workflow state contract

The private artifact root should contain **one** tiny workflow-state file so that any new session can recover the position cheaply. This is a **contract**, not a file in this repository; it is created in the private environment by the helpers.

- If an equivalent file already exists there (for example `SESSION_LOG` or a similar file that records harness phases), helpers **detect and use it** instead of creating a competing file.
- Otherwise they create `STATE.md` with exactly these fields:

```text
Current major phase: H01 - Inventory Existing Constructs
Last completed major phase: NONE
Status: COMPLETED
Last operation: HFREEZE - phase frozen
Next operation: HRECOVER - Fresh Session Recovery Test
Next major phase: H02 - Historical Usage Mining
Next session: NEW
Primary artifact: 01-inventory.md
Handoff: handoff/01-handoff.md
Blocking items: none
Last updated: <date>
```

- `Status` values: `NOT STARTED`, `IN PROGRESS`, `COMPLETED` (finished, completeness-checked and frozen), `VALIDATED` (a fresh-session recovery test returned SUFFICIENT).
- It is **not** a knowledge base, a conversation summary, or a history. Anything longer belongs in the phase report or the handoff file.
- Which helper writes what: HCHECK, HFREEZE, HCHECKPOINT, HRECOVER, HREPAIR and HREVIEW update the file; HNEXT only reads it. If the file and the artifacts disagree, the artifacts win and the disagreement is reported.

## 6. Maintaining the protocol

When you add, rename or reorder a prompt:

1. Update the catalog rows and workflow map.
2. Update the phase table everywhere it is embedded. It appears in the canonical detection block above and is copied verbatim into HCHECK, HFREEZE, HCHECKPOINT, HRECOVER, HREPAIR, HREVIEW and HNEXT (HBENCH carries a shorter lookup rule instead). HCHECK also embeds the expected-contents list; HFREEZE embeds the next-operation rules; HNEXT embeds the workflow sequence.
3. Keep the self-identification headers of the affected major prompts in sync (previous/next phase and primary artifact).
4. Never reuse an ID. Retire it in the catalog instead.
5. Check that no helper tells Claude to open a public file name, and that every catalog link resolves.
