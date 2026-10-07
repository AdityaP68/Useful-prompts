# Runbook: Operating the Harness Prompts by Hand

This runbook tells you how to execute the major prompts manually: GitHub → copy a prompt → paste it into Claude Code → Claude executes it → come back for the next one. It adds a thin layer of **gates, session boundaries and helper prompts** around the major prompts. It does not copy or change them.

## Two worlds, one bridge

| | Public prompt library (this repo) | Private Claude Code environment |
|---|---|---|
| What lives there | Prompt files, this runbook, the catalog | Reports, handoffs, workflow state, your code |
| Who uses it | **You** | **Claude** |
| Names used | File names, catalog links | **Logical IDs** (`H01`, `HCHECK`) and private artifact paths |

Private Claude **cannot** open anything in this repository and does not know what a file name here means. Every pasted prompt therefore identifies itself with a logical ID, and phases communicate only through private artifacts. When Claude says "run HCHECK", you look `HCHECK` up in **[PROMPT-CATALOG.md](PROMPT-CATALOG.md)**, open the linked file, copy, paste. Spec: [PROTOCOL.md](PROTOCOL.md).

- Major prompts: [H01](01-inventory-existing-constructs.md) · [H02](02-mine-historical-usage.md) · [H03](03-design-context-hierarchy.md) · [H04](04-discover-skills.md) · [H05](05-discover-subagents.md) · [H06](06-audit-hooks-and-mcps.md) · [H07](07-synthesize-native-harness.md) · [H08](08-implement-p0.md) · [H09](09-adversarial-review.md) · [H10](10-benchmark-harness.md)
- Helpers: [HCHECK](helpers/phase-completeness-check.md) · [HFREEZE](helpers/freeze-phase.md) · [HCHECKPOINT](helpers/checkpoint.md) · [HRECOVER](helpers/fresh-session-recovery-test.md) · [HREPAIR](helpers/repair-handoff.md) · [HSCOPE](helpers/scope-guard.md) · [HDIAG](helpers/diagnostic-only.md) · [HHISTORY](helpers/safe-historical-analysis.md) · [HEVAL](helpers/construct-evaluator.md) · [HBENCH](helpers/record-benchmark.md) · [HREVIEW](helpers/independent-review-handoff.md) · [HNEXT](helpers/what-next.md)
- Status tracking and prompt maturity: [LIFECYCLE.md](LIFECYCLE.md)

The harness does not exist yet; these prompts are how you analyze and construct it. The gates below test that **persisted private artifacts, not long conversations, carry the work**, so any session is disposable.

**Copying a major prompt:** open the file and copy from the `HARNESS WORKFLOW` line to the end of the file (copying the whole file also works). **Copying a helper:** copy the block under "Copy/Paste Prompt". Helpers normally need no editing.

---

## 0. One-time setup

1. Pick the artifact root, **`AUDIT_DIR`**: a folder **outside every repository** (for example a folder in your user profile). Everything the prompts produce goes here. It is private working material, may contain real identifiers, and must never be committed to a shared or public repository.
2. Decide where you launch Claude Code: normally from `WORKSPACE` (the folder containing the repositories), or from a single repository if that is all you use.
3. Know your environment: native Windows, WSL, macOS or Linux (see the Windows notes in the [README](README.md#windows-notes)).

You do **not** create any state file yourself. The helpers create and maintain a tiny workflow-state file (`STATE.md`, or they use an equivalent that already exists) in `AUDIT_DIR`.

## 1. Start of every session (paste this first)

A fresh session does not know your placeholders. Paste this before the first prompt of every session, filled in. Copy it from here; it is not a separate file.

```text
Placeholders for this session:
AUDIT_DIR = <path outside every repository>
WORKSPACE = <the directory I launched from>
Environment = <native Windows (PowerShell/cmd/Git Bash) | WSL | macOS | Linux>

Unless the prompt I paste next says otherwise: do not modify any repository, any
Claude Code configuration, or any history file; write only under AUDIT_DIR; never
print secrets; ask me if anything is ambiguous instead of guessing.
```

Then paste the major prompt or helper. Helpers can also find `AUDIT_DIR` on their own, but stating it removes a source of doubt.

If a helper says it cannot tell which phase you mean, paste the optional **`HARNESS CONTEXT`** block from [PROMPT-CATALOG.md](PROMPT-CATALOG.md#if-claude-cannot-tell-which-phase-you-mean) with the phase edited. You normally do not need it.

## 2. The standard gate (after every analysis phase H01–H07)

```
run the phase
   ↓
HCHECK  — Phase Completeness Check          (SAME session)
   ↓  COMPLETE (otherwise fix, then HCHECK again)
HFREEZE — Freeze Current Phase              (SAME session)
   ↓  self-contained artifact + handoff + workflow state on disk
Is the conversation large?
   │
   ├── NO, and HFREEZE said the next operation is the next phase
   │       → paste the next major prompt in this session
   │
   └── YES (HFREEZE says HRECOVER, NEW) → start a NEW session
          ↓
       HRECOVER — Fresh Session Recovery Test
          ↓
       RECOVERY: SUFFICIENT?
          │
          ├── NO  → HREPAIR (original session if open, else new)
          │            ↓
          │         new session, HRECOVER again
          │
          └── YES → the state file now says VALIDATED
                     ↓
                  (optional) HBENCH — record what the recovery cost
                     ↓
                  paste the NEXT major prompt in this same session
```

**Is it large?** HFREEZE decides and records its recommendation in the state file: it treats the conversation as large if it is past roughly half of the context window, has been compacted, or it is unsure, and always asks for a recovery test after H01 and H06. You can override: when in doubt, start new. That is the point of the exercise.

**If you must stop early:** paste [HCHECKPOINT](helpers/checkpoint.md) before clearing or closing the session. After a break, or if you lose track, paste [HNEXT](helpers/what-next.md). It answers in logical IDs; look them up in the catalog.

## 3. Session model

| Stage | Session recommendation | Why |
|---|---|---|
| H01 → H07 discovery/design | One session per phase, or a few phases per session while context is small. HFREEZE, then new session + HRECOVER when large. | Tests that artifacts carry the knowledge |
| H07 → approval | Review `07-harness-design.md` yourself and record decisions | Human gate; nothing is implemented without it |
| H10 baseline round | Each task run in its own fresh, normal-usage session | Measures realistic usage |
| H08 implementation | **Fresh session**, from the approved persisted design only | Proves the design is self-sufficient; limits blast radius |
| H09 review | **Separate fresh, independent session**, never the implementer's | Independence |
| H10 harness round | Each task run in its own fresh, normal-usage session | Comparable with the baseline |

## 4. Interruptions: which helper

| Symptom | Paste |
|---|---|
| Claude is editing config, creating files outside `AUDIT_DIR`, or redesigning during an audit | [HSCOPE](helpers/scope-guard.md) |
| You want a no-write guarantee for a session | [HDIAG](helpers/diagnostic-only.md) |
| Claude opens huge transcripts or prints raw history | [HHISTORY](helpers/safe-historical-analysis.md) |
| A candidate skill/agent/hook/script/MCP/rule is borderline | [HEVAL](helpers/construct-evaluator.md) |
| You returned after a break or lost track | [HNEXT](helpers/what-next.md) |
| Session is getting heavy mid-phase | [HCHECKPOINT](helpers/checkpoint.md) |

---

## 5. Per-phase runbook

Each entry: **BEFORE** → **RUN** → **WHILE RUNNING** → **DONE WHEN** → **AFTER**. "Mutation" refers to what the major prompt allows. Private artifact names are relative to `AUDIT_DIR`.

### H01 — Inventory Existing Constructs

**BEFORE**
- `AUDIT_DIR` chosen (section 0). No earlier artifact is needed.
- Session: **NEW**, launched from `WORKSPACE` (or the single repository). Paste the section 1 preamble first.
- Mutation: **report only** (`01-inventory.md`).

**RUN:** [H01 — 01-inventory-existing-constructs.md](01-inventory-existing-constructs.md)

**WHILE RUNNING**
- Expect: version and documentation checks, a scan of user, workspace and repository configuration, a large inventory table.
- Do not allow: edits to configuration or repositories, printing of secrets, reading whole source trees, starting or stopping MCP servers.
- Helpers: [HSCOPE](helpers/scope-guard.md), [HDIAG](helpers/diagnostic-only.md).

**DONE WHEN**
- `01-inventory.md` exists with its sections: environment/version facts, scope map, construct inventory with verdicts, always-loaded context per launch location, conflicts/duplication/stale items, portability, verdict counts, open questions, hand-off notes for the next phase.
- Claude ended with "H01 complete. Recommended next operation: HCHECK" and did not propose a new design.

**AFTER**
1. [HCHECK](helpers/phase-completeness-check.md)
2. [HFREEZE](helpers/freeze-phase.md)
3. If HFREEZE says so: **NEW session** → [HRECOVER](helpers/fresh-session-recovery-test.md); on INSUFFICIENT → [HREPAIR](helpers/repair-handoff.md) → HRECOVER again in a new session.
4. Next: **H02**.

#### The H01 → H02 transition

```
H01  Inventory Existing Constructs
        ↓
HCHECK   Phase Completeness Check
        ↓
HFREEZE  Freeze Current Phase
        ↓
self-contained persisted inventory + handoff + workflow state
        ↓
if current conversation/context is large:
    start NEW Claude session
        ↓
HRECOVER  Fresh Session Recovery Test
        ↓
    recovery sufficient?
        │
        ├── NO → HREPAIR  Repair Handoff
        │          ↓
        │       retry HRECOVER (new session)
        │
        └── YES
              ↓
H02  Historical Usage Mining
```

Concretely, by hand:
1. In the H01 session, paste **HCHECK** (no editing). Fix anything it lists, then paste it again until it says `COMPLETENESS: COMPLETE`.
2. Paste **HFREEZE**. Wait for `READY TO CLEAR`. It tells you the recommended next operation by logical ID (typically `HRECOVER`, NEW session).
3. Open a new session. Paste the section 1 preamble, then **HRECOVER**. Claude finds the artifact root and workflow state on its own and reports `RECOVERED STATE`.
4. `RECOVERY: SUFFICIENT` → paste **HHISTORY**, then paste **H02** in that same session.
5. `RECOVERY: INSUFFICIENT` → close that session. Paste **HREPAIR** into the original session if it is still open, otherwise into a new one; then paste **HRECOVER** in another new session.

**If you ran H01 before this protocol existed** (no workflow-state file yet): that is fine. HCHECK detects the phase from `01-inventory.md` and creates the state file; HFREEZE completes it. You never need the H01 prompt text or this repository in the private session.

### H02 — Historical Usage Mining

**BEFORE**
- `01-inventory.md` and `handoff/01-handoff.md` exist, and the state file says H01 is VALIDATED.
- Session: **NEW** (the HRECOVER session is ideal).
- Mutation: **artifacts only** (a throwaway analysis script, aggregate files, `02-history-findings.md`). History files are read-only.

**RUN:** [H02 — 02-mine-historical-usage.md](02-mine-historical-usage.md), after pasting [HHISTORY](helpers/safe-historical-analysis.md).

**WHILE RUNNING**
- Expect: history location/size discovery, schema sampling, a script written under `AUDIT_DIR`, statistics, workflow clusters, then validation against the current environment.
- Do not allow: reading transcript files in full, pasting raw conversation text, printing credentials, modifying or deleting history, any repository change.
- Helpers: HHISTORY, [HSCOPE](helpers/scope-guard.md).

**DONE WHEN**
- `02-history-findings.md` has its eight sections; each finding has a frequency, time spread and classification; the evidence threshold is stated; below-threshold items are in "Observed but not justified"; findings are marked VERIFIED/STALE/UNVERIFIED.
- The index script is saved under `AUDIT_DIR` for reuse by H10.

**AFTER:** HCHECK → HFREEZE → new session + HRECOVER → **H03**. (The H02 session is usually large.)

### H03 — Context Hierarchy Design

**BEFORE:** H01 and H02 artifacts and handoffs exist; H02 VALIDATED. Session: **NEW** (the HRECOVER session). Mutation: report only (`03-hierarchy.md`).

**RUN:** [H03 — 03-design-context-hierarchy.md](03-design-context-hierarchy.md)

**WHILE RUNNING**
- Expect: reading the earlier reports, targeted inspection of manifests and docs, a level-by-level design.
- Do not allow: creating any `CLAUDE.md`, rule or skill (design only), copying whole documents into the design, recording facts discoverable from code.
- Helpers: HSCOPE, [HEVAL](helpers/construct-evaluator.md) (`CLAUDE.MD/RULE`).

**DONE WHEN:** `03-hierarchy.md` has its sections including the level-by-level spec, precedence rules with documentation references, the knowledge lifecycle, task-state design, migration map with before/after context estimates, and "things deliberately not documented".

**AFTER:** HCHECK → HFREEZE → (new session + HRECOVER if large) → **H04**.

### H04 — Skill Discovery

**BEFORE:** H01–H03 artifacts exist; H03 frozen. Session: **NEW** preferred (same session acceptable if the H03 conversation is small). Mutation: report only; any drafts go under `AUDIT_DIR`, **never** into real skill directories.

**RUN:** [H04 — 04-discover-skills.md](04-discover-skills.md)

**WHILE RUNNING**
- Expect: checks of installed Skill tooling, evaluation of existing Skills, evidence-backed candidates with overlap analysis.
- Do not allow: creating skills in real locations, candidates without historical evidence, many narrow or one-command skills.
- Helpers: HEVAL (`SKILL`) for borderline candidates, HSCOPE.

**DONE WHEN:** `04-skill-candidates.md` has its sections: each candidate with all required fields, an overlap matrix, P0/P1/P2 ranking, rejected candidates with reasons, and a test plan for P0 skills.

**AFTER:** HCHECK → HFREEZE → **H05**.

### H05 — Subagent Discovery

**BEFORE:** H01–H04 artifacts exist. Session: NEW or same as H04 if light. Mutation: report only.

**RUN:** [H05 — 05-discover-subagents.md](05-discover-subagents.md)

**WHILE RUNNING**
- Expect: evaluation of existing agents and observed subagent usage, an agent-vs-skill-vs-script decision table.
- Do not allow: invented generic agents, any agent definition written to disk.
- Helpers: HEVAL (`SUBAGENT`), HSCOPE.

**DONE WHEN:** `05-subagent-candidates.md` exists with candidates (possibly none) with evidence and output contracts, the decision table, a minimal set, and rejected candidates. An empty P0 list is a valid result.

**AFTER:** HCHECK → HFREEZE → **H06**.

### H06 — Hooks and MCP Audit

**BEFORE:** H01–H05 artifacts exist. Session: NEW or same as H05 if light. Mutation: report only.

**RUN:** [H06 — 06-audit-hooks-and-mcps.md](06-audit-hooks-and-mcps.md)

**WHILE RUNNING**
- Expect: reading hook scripts and MCP configuration, listing servers and tools, estimating tool-definition overhead.
- Do not allow: editing settings or hook scripts, printing credentials from configuration, starting/stopping/reconfiguring servers beyond read-only listing, adding custom code-intelligence servers.
- Helpers: HEVAL (`HOOK`, `MCP`, `SCRIPT`), HDIAG, HSCOPE.

**DONE WHEN:** `06-hooks-and-mcps.md` has its sections: hook and MCP inventories with verdicts, candidates to add, overhead before/after, and security/portability findings.

**AFTER:** HCHECK → HFREEZE → **NEW session + HRECOVER (always, here)** → **H07**. H07 consumes all six reports, so it is the real test that the artifacts are sufficient.

### H07 — Native Harness Synthesis

**BEFORE:** H01–H06 artifacts and handoffs exist; H06 VALIDATED. Session: **NEW**, working from the artifacts only. Mutation: report only (`07-harness-design.md`).

**RUN:** [H07 — 07-synthesize-native-harness.md](07-synthesize-native-harness.md)

**WHILE RUNNING**
- Expect: reconciliation of conflicts between earlier reports, the eight-field specification for every construct, a P0/P1/P2 plan, and a section on problems native constructs cannot solve.
- Do not allow: implementation, building of custom indexers/graphs/routers, invented Claude Code features (anything uncertain must be labelled UNVERIFIED).
- Helpers: HEVAL, HSCOPE.

**DONE WHEN:** `07-harness-design.md` has its sections, P0 items list exact files to be created/changed, and Claude has printed the executive summary and the approval checklist and asked which P0 items are approved.

**AFTER**
1. HCHECK → HFREEZE (its next operation will be HUMAN APPROVAL).
2. **HUMAN APPROVAL GATE.** Read the design yourself. For each P0 item decide APPROVED / REJECTED / MODIFIED. Tell Claude your decisions and ask it to write them verbatim to `07-approvals.md` in `AUDIT_DIR`. Nothing is implemented without this file.
3. Run the **baseline benchmark round** (H10, below) before any implementation.
4. Then **H08**, in a fresh session.

### H08 — P0 Implementation

**BEFORE**
- `07-harness-design.md`, `07-approvals.md` and the baseline benchmark records exist.
- Git state of affected repositories is understood (clean, or you know why not).
- Session: **FRESH**, from persisted artifacts only. Mutation: **APPROVAL REQUIRED**: the prompt produces a change manifest and waits for you before writing anything.

**RUN:** [H08 — 08-implement-p0.md](08-implement-p0.md). In your first message, tell it the approved list is in `07-approvals.md`.

**WHILE RUNNING**
- Expect: preflight (Git recoverability, backups outside repositories), the change manifest, a pause for your confirmation, then implementation and validation.
- Do not allow: application source changes, secrets, edits to shared/team-wide/managed configuration you did not approve, commits or pushes, items that were not approved.
- Read the manifest carefully before confirming. This is the one phase that changes things.
- Helpers: HSCOPE, HDIAG (until you confirm the manifest).

**DONE WHEN:** `08-implementation-report.md` lists changed files, the effective hierarchy, validation results, conflicts, before/after always-loaded context, and rollback instructions, and Claude has not committed or pushed.

**AFTER**
1. [HCHECK](helpers/phase-completeness-check.md)
2. [HREVIEW](helpers/independent-review-handoff.md) (writes the neutral review brief)
3. [HFREEZE](helpers/freeze-phase.md)
4. Start a **separate, fresh session** for **H09**. Do not reuse the implementation session.

### H09 — Adversarial Review

**BEFORE:** the implemented configuration, `07-harness-design.md`, `08-implementation-report.md` and `handoff/09-review-brief.md` exist. Session: **FRESH and independent**. Mutation: report only (`09-review.md`).

**RUN:** [H09 — 09-adversarial-review.md](09-adversarial-review.md), then paste the line HREVIEW gave you ("Read handoff/09-review-brief.md in the harness artifact root first. Treat every claim … as unverified.").

**WHILE RUNNING**
- Expect: file inspection, context-report probes in `WORKSPACE` and in each repository, skill trigger/near-miss tests, and a ranked findings list biased toward removal.
- Do not allow: applying fixes, editing configuration to make a probe pass.
- Helpers: HDIAG (recommended for the whole session), HEVAL, HSCOPE.

**DONE WHEN:** `09-review.md` has its sections, with findings ranked CRITICAL/HIGH/MEDIUM/LOW, each with evidence, a failure scenario and a recommended action; CRITICAL and HIGH findings were printed.

**AFTER**
1. HCHECK → HFREEZE (its next operation will be HUMAN DECISION).
2. **You** decide which findings to apply. Ask Claude to record the decision in `09-decisions.md`.
3. Apply approved fixes using the same discipline as H08 (fresh session, manifest, confirmation, rollback). Do not skip the manifest even for deletions.
4. Then the **harness benchmark round** (H10).

### H10 — Harness Benchmark

H10 is run **twice**: a **baseline round** (after H07 approval, before H08) and a **harness round** (after H09's fixes).

**BEFORE**
- Baseline round: `07-harness-design.md`, `07-approvals.md`, and the index script from H02. Harness round: fixes from H09 applied.
- Use a scratch branch or worktree (short path on Windows) that you can discard. Never benchmark on a branch holding work you need.
- Session: see the manual flow below. Mutation: scratch worktree only, plus records under `10-benchmark/` in `AUDIT_DIR`.

**RUN:** [H10 — 10-benchmark-harness.md](10-benchmark-harness.md)

**Manual flow** (H10 describes Claude driving the runs; operated by hand, you drive them):
1. **Planning session (NEW).** Run H10 through its task-set and conditions steps only: define the tasks and conditions, with expected outcomes and automated verification fixed *before* any run. HFREEZE it, so the task list is on disk and identical for both rounds.
2. **Each task run (NEW session per run, normal usage).** Reset the scratch starting point, start a fresh session in the normal launch directory, and give the task's exact prompt with no extra hints and no runbook preamble beyond what you would normally use. Run the task's verification yourself and note PASS/FAIL.
3. **After each run:** [HBENCH](helpers/record-benchmark.md), preferably in another new session so the recording cost is not counted.
4. **Analysis session (NEW).** Run H10's analysis and decision steps over the records: cost per successfully verified task, secondary metrics, construct contribution, remaining bottlenecks.

**WHILE RUNNING**
- Expect: noisy single runs (do several if budget allows), tasks that fail.
- Do not allow: tuning the harness between runs of the same round, hints in task prompts, pushing, or touching shared branches.
- Helpers: HBENCH, HSCOPE.

**DONE WHEN:** the baseline and harness rounds each have the same task set run under the same settings, every run has a record and a verification result, and `10-benchmark/report.md` contains results with spread, remaining bottlenecks classified as native-solvable or later-phase requirements, and recommendations.

**AFTER:** HFREEZE. Decide with the data what to keep, simplify or remove. Only if large, repeated, native-unaddressable bottlenecks remain should the later optional code-intelligence phase be considered.

---

## 6. Troubleshooting

- **A new session doesn't know where your files are.** Paste the section 1 preamble. Helpers also search for the workflow-state file from the current directory upward and ask you if they find none or several.
- **A helper says it cannot determine the phase.** Paste the optional `HARNESS CONTEXT` block from the catalog, or run [HNEXT](helpers/what-next.md).
- **Claude says it cannot find a prompt, file or ID.** It should never need to: IDs are labels, not files. If it asks for an external file, tell it that no prompt library is available in this environment and to use the persistent artifacts only.
- **HRECOVER keeps failing for the same reason.** The phase artifact is probably missing the content; put the material into the artifact (not only the handoff) during HREPAIR. If the cause looks like a flaw in a major prompt, do not edit it: write it up in [PROPOSED-CHANGES.md](PROPOSED-CHANGES.md).
- **A phase went wrong.** HSCOPE, then inspect with read-only commands. H08 recorded rollback steps for everything it changed; other phases changed nothing outside `AUDIT_DIR`.
- **Two state files.** If you see both `STATE.md` and another file that records harness phases, keep the one that already existed, delete nothing automatically, and tell Claude which one is authoritative.
- **Windows specifics.** See the README's Windows notes: native vs WSL have separate histories and configuration locations.
