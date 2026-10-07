# Runbook: Operating the Harness Prompts by Hand

This runbook tells you how to execute the existing major prompts manually: GitHub → copy a prompt → paste it into Claude Code → Claude executes it → come back for the next one. It adds a thin layer of **gates, session boundaries and helper prompts** around the prompts. It does not copy or change them.

- Major prompts: [01](01-inventory-existing-constructs.md) · [02](02-mine-historical-usage.md) · [03](03-design-context-hierarchy.md) · [04](04-discover-skills.md) · [05](05-discover-subagents.md) · [06](06-audit-hooks-and-mcps.md) · [07](07-synthesize-native-harness.md) · [08](08-implement-p0.md) · [09](09-adversarial-review.md) · [10](10-benchmark-harness.md)
- Helpers: [index](helpers/README.md)
- Status tracking and prompt maturity: [LIFECYCLE.md](LIFECYCLE.md)

The harness does not exist yet; these prompts are how you analyze and construct it. The aim of the gates below is to prove that **persisted artifacts, not long conversations, carry the work**, so that any session is disposable.

---

## 0. One-time setup

1. Pick `AUDIT_DIR`: a folder **outside every repository** (for example a folder in your user profile). Everything the prompts produce goes here. It is local working material, may contain real identifiers, and must never be committed to a shared or public repository.
2. Create the local status file `AUDIT_DIR/STATUS.md` using the template in [LIFECYCLE.md](LIFECYCLE.md#local-status-file). Keep it out of Git.
3. Decide where you launch Claude Code: normally from `WORKSPACE` (the folder containing the repositories), or from a single repository if that is all you use.
4. Know your environment: native Windows, WSL, macOS or Linux (see the Windows notes in the [README](README.md#windows-notes)).

## 1. Start of every session (paste this first)

A fresh session does not know your placeholders. Paste this before the first prompt of every session, filling it in. Copy it from here; it is not a separate file.

```text
Placeholders for this session:
AUDIT_DIR = <path outside every repository>
WORKSPACE = <the directory I launched from>
Environment = <native Windows (PowerShell/cmd/Git Bash) | WSL | macOS | Linux>

Unless the prompt I paste next says otherwise: do not modify any repository, any
Claude Code configuration, or any history file; write only under AUDIT_DIR; never
print secrets; ask me if anything is ambiguous instead of guessing.
```

Then paste the major prompt (or helper). If a prompt tells Claude to ask you for `AUDIT_DIR`, this preamble already answers it.

## 2. The standard gate (after every analysis phase 01–07)

```
run the phase
   ↓
Phase Completeness Check          (helpers/phase-completeness-check.md, SAME session)
   ↓  COMPLETE (otherwise fix, then re-check)
Freeze Phase                      (helpers/freeze-or-checkpoint.md, MODE: PHASE-COMPLETE)
   ↓  self-contained report + handoff on disk
Is the conversation large?
   │
   ├── NO, and you are continuing now → you may run the next prompt in this session
   │      (still do the recovery test at least at the 01→02 and 07→08 boundaries)
   │
   └── YES (see "Is it large?" below) → start a NEW session
          ↓
       Fresh Session Recovery Test  (helpers/fresh-session-recovery-test.md)
          ↓
       RECOVERY: SUFFICIENT?
          │
          ├── NO  → Repair Handoff (helpers/repair-handoff.md)
          │            ↓
          │         new session, run the recovery test again
          │
          └── YES → set the phase to VALIDATED in STATUS.md
                     ↓
                  run the NEXT major prompt in this same session
```

**Is it large?** Use the product's own context report. Treat the conversation as large if it is past roughly half of the window, if it has been compacted automatically, or if you are unsure. When in doubt, freeze and start new: that is the point of the exercise.

**If you must stop early:** use [Freeze / Checkpoint](helpers/freeze-or-checkpoint.md) with `MODE: MID-PHASE-CHECKPOINT` before clearing or closing the session. After a break, run [What Should I Do Next?](helpers/what-next.md).

## 3. Session model

| Stage | Session recommendation | Why |
|---|---|---|
| 01 → 07 discovery/design | One session per phase, or a few phases per session while context is small. Freeze, then new session + recovery test when large. | Tests that artifacts carry the knowledge |
| 07 → approval | Review `07-harness-design.md` yourself; record decisions | Human gate; nothing is implemented without it |
| 10 baseline round | Each task run in its own fresh, normal-usage session | Measures realistic usage |
| 08 implementation | **Fresh session**, from the approved persisted design only | Proves the design is self-sufficient; limits blast radius |
| 09 review | **Separate fresh, independent session**, never the implementer's | Independence |
| 10 harness round | Each task run in its own fresh, normal-usage session | Comparable with the baseline |

## 4. Interruptions: which helper

| Symptom | Use |
|---|---|
| Claude is editing config, creating files outside `AUDIT_DIR`, or redesigning during an audit | [Stop Scope Creep](helpers/guardrails.md) |
| You want a no-write guarantee for a session | [Diagnostic Only](helpers/guardrails.md) |
| Claude opens huge transcripts or prints raw history | [Safe Historical Analysis](helpers/safe-historical-analysis.md) |
| A candidate skill/agent/hook/script/MCP/rule is borderline | [Construct Evaluator](helpers/construct-evaluator.md) |
| You returned after a break or lost track | [What Should I Do Next?](helpers/what-next.md) |
| Session is getting heavy mid-phase | [Freeze / Checkpoint](helpers/freeze-or-checkpoint.md) (`MID-PHASE-CHECKPOINT`) |

---

## 5. Per-prompt runbook

Each entry: **BEFORE** → **RUN** → **WHILE RUNNING** → **DONE WHEN** → **AFTER**. "Mutation" refers to what the major prompt itself allows. Report file names are the ones the major prompts specify.

### Prompt 01: Inventory existing constructs

**BEFORE**
- `AUDIT_DIR` and `STATUS.md` exist (section 0). No earlier artifact is needed.
- Session: **NEW**, launched from `WORKSPACE` (or the single repository). Paste the section 1 preamble first.
- Mutation: **report only** (`01-inventory.md`).

**RUN:** [01-inventory-existing-constructs.md](01-inventory-existing-constructs.md)

**WHILE RUNNING**
- Expect: version and documentation checks, a scan of user, workspace and repository configuration, a large inventory table.
- Do not allow: edits to configuration or repositories, printing of secrets, reading whole source trees, starting or stopping MCP servers.
- Helpers: Stop Scope Creep, Diagnostic Only.

**DONE WHEN**
- `AUDIT_DIR/01-inventory.md` exists with its nine sections: environment/version facts, scope map, construct inventory with verdicts, always-loaded context per launch location, conflicts/duplication/stale items, portability, verdict counts, open questions, and hand-off notes for 02.
- Claude printed a short summary and did not propose a new design.

**AFTER**
1. [Phase Completeness Check](helpers/phase-completeness-check.md)
2. [Freeze Phase](helpers/freeze-or-checkpoint.md) (`PHASE-COMPLETE`, `NEXT_PROMPT: 02-mine-historical-usage.md`)
3. If the conversation is large: **NEW session** → [Fresh Session Recovery Test](helpers/fresh-session-recovery-test.md); on INSUFFICIENT → [Repair Handoff](helpers/repair-handoff.md) → retest in a new session.
4. Next: **02**.

#### The 01 → 02 transition (the currently important flow)

```
PROMPT 01  Inventory Existing Constructs
        ↓
Phase Completeness Check
        ↓
Freeze Current Phase
        ↓
self-contained persisted inventory + handoff
        ↓
if current conversation/context is large:
    start NEW Claude session
        ↓
Fresh Session Recovery Test
        ↓
    recovery sufficient?
        │
        ├── NO → Repair Handoff
        │          ↓
        │       retry recovery (new session)
        │
        └── YES
              ↓
PROMPT 02  Historical Usage Mining
```

Concretely, by hand:
1. In the 01 session, paste the Completeness Check. Fix anything it lists.
2. Paste Freeze Phase (`PHASE: 01 Inventory existing constructs`, `NEXT_PROMPT: 02-mine-historical-usage.md`). Wait for `READY TO CLEAR` and note the text it gives you for the next session.
3. Open a new session. Paste the section 1 preamble, then the Recovery Test (`FROZEN_PHASE: 01`).
4. `RECOVERY: SUFFICIENT` → mark 01 `VALIDATED` in `STATUS.md`, paste the Safe Historical Analysis helper, then paste prompt 02 in that same session.
5. `RECOVERY: INSUFFICIENT` → close that session, paste Repair Handoff (with the gap list) into the original session if it is still open, otherwise into a new one; then repeat step 3 in another new session.

### Prompt 02: Historical usage mining

**BEFORE**
- `01-inventory.md` and `handoff/01-handoff.md` exist and 01 is VALIDATED.
- Session: **NEW** (the recovery-test session is ideal).
- Mutation: **artifacts only** (a throwaway analysis script, aggregate files, `02-history-findings.md`). History files are read-only.

**RUN:** [02-mine-historical-usage.md](02-mine-historical-usage.md), after pasting [Safe Historical Analysis](helpers/safe-historical-analysis.md).

**WHILE RUNNING**
- Expect: history location/size discovery, schema sampling, a script written under `AUDIT_DIR`, statistics, workflow clusters, then validation against the current environment.
- Do not allow: reading transcript files in full, pasting raw conversation text, printing credentials, modifying or deleting history, any repository change.
- Helpers: Safe Historical Analysis, Stop Scope Creep.

**DONE WHEN**
- `02-history-findings.md` has its eight sections, each finding has a frequency, time spread and classification, an evidence threshold is stated, below-threshold items are in "Observed but not justified", and findings are marked VERIFIED/STALE/UNVERIFIED against today's environment.
- The index script is saved under `AUDIT_DIR` for reuse by prompt 10.

**AFTER:** Completeness Check → Freeze (`NEXT_PROMPT: 03-design-context-hierarchy.md`) → new session + Recovery Test → **03**. (The 02 session is usually large; start new.)

### Prompt 03: Context and memory hierarchy

**BEFORE:** `01` and `02` reports and handoffs exist; 02 VALIDATED. Session: **NEW** (recovery-test session). Mutation: report only (`03-hierarchy.md`).

**RUN:** [03-design-context-hierarchy.md](03-design-context-hierarchy.md)

**WHILE RUNNING**
- Expect: reading the earlier reports, targeted inspection of manifests and docs, a level-by-level design.
- Do not allow: creating any `CLAUDE.md`, rule or skill (design only), copying whole documents into the design, recording facts discoverable from code.
- Helpers: Stop Scope Creep, Construct Evaluator (`CLAUDE.MD/RULE`).

**DONE WHEN:** `03-hierarchy.md` has its ten sections including the level-by-level spec, precedence rules with documentation references, the knowledge lifecycle, task-state design, migration map with before/after context estimates, and "things deliberately not documented".

**AFTER:** Completeness Check → Freeze (`NEXT_PROMPT: 04-discover-skills.md`) → new session + Recovery Test if large → **04**.

### Prompt 04: Skill discovery

**BEFORE:** `01`–`03` reports exist; 03 frozen. Session: **NEW** preferred (same session acceptable if the 03 conversation is small). Mutation: report only; any drafts go under `AUDIT_DIR`, **never** into real skill directories.

**RUN:** [04-discover-skills.md](04-discover-skills.md)

**WHILE RUNNING**
- Expect: checks of installed Skill tooling, evaluation of existing Skills, evidence-backed candidates with overlap analysis.
- Do not allow: creating skills in real locations, candidates without historical evidence, many narrow or one-command skills.
- Helpers: Construct Evaluator (`SKILL`) for borderline candidates, Stop Scope Creep.

**DONE WHEN:** `04-skill-candidates.md` has its seven sections: each candidate with all required fields, an overlap matrix, P0/P1/P2 ranking, rejected candidates with reasons, and a test plan for P0 skills.

**AFTER:** Completeness Check → Freeze (`NEXT_PROMPT: 05-discover-subagents.md`) → **05**.

### Prompt 05: Subagent discovery

**BEFORE:** `01`–`04` reports exist. Session: NEW or same as 04 if light. Mutation: report only.

**RUN:** [05-discover-subagents.md](05-discover-subagents.md)

**WHILE RUNNING**
- Expect: evaluation of existing agents and observed subagent usage, an agent-vs-skill-vs-script decision table.
- Do not allow: invented generic agents, any agent definition written to disk.
- Helpers: Construct Evaluator (`SUBAGENT`), Stop Scope Creep.

**DONE WHEN:** `05-subagent-candidates.md` exists with candidates (possibly none) with evidence and output contracts, the decision table, a minimal set, and rejected candidates. An empty P0 list is a valid result.

**AFTER:** Completeness Check → Freeze (`NEXT_PROMPT: 06-audit-hooks-and-mcps.md`) → **06**.

### Prompt 06: Hooks and MCP audit

**BEFORE:** `01`–`05` reports exist. Session: NEW or same as 05 if light. Mutation: report only.

**RUN:** [06-audit-hooks-and-mcps.md](06-audit-hooks-and-mcps.md)

**WHILE RUNNING**
- Expect: reading hook scripts and MCP configuration, listing servers and tools, estimating tool-definition overhead.
- Do not allow: editing settings or hook scripts, printing credentials from configuration, starting/stopping/reconfiguring servers beyond read-only listing, adding custom code-intelligence servers.
- Helpers: Construct Evaluator (`HOOK`, `MCP`, `SCRIPT`), Diagnostic Only, Stop Scope Creep.

**DONE WHEN:** `06-hooks-and-mcps.md` has its eight sections: hook and MCP inventories with verdicts, candidates to add, overhead before/after, and security/portability findings.

**AFTER:** Completeness Check → Freeze (`NEXT_PROMPT: 07-synthesize-native-harness.md`) → **NEW session + Recovery Test (always, here)** → **07**. 07 consumes all six reports, so it is the real test that the artifacts are sufficient.

### Prompt 07: Native harness synthesis

**BEFORE:** `01`–`06` reports and handoffs exist; 06 VALIDATED. Session: **NEW**, working from the artifacts only. Mutation: report only (`07-harness-design.md`).

**RUN:** [07-synthesize-native-harness.md](07-synthesize-native-harness.md)

**WHILE RUNNING**
- Expect: reconciliation of conflicts between earlier reports, the eight-field specification for every construct, a P0/P1/P2 plan, and a section on problems native constructs cannot solve.
- Do not allow: implementation, building of custom indexers/graphs/routers, invented Claude Code features (anything uncertain must be labelled UNVERIFIED).
- Helpers: Construct Evaluator, Stop Scope Creep.

**DONE WHEN:** `07-harness-design.md` has its ten sections, P0 items list exact files to be created/changed, and Claude has printed the executive summary and the approval checklist and asked which P0 items are approved.

**AFTER**
1. Completeness Check → Freeze (`NEXT_PROMPT: 10 baseline round, then 08-implement-p0.md`).
2. **HUMAN APPROVAL GATE.** Read the design yourself. For each P0 item decide APPROVED / REJECTED / MODIFIED. Tell Claude your decisions and ask it to write them verbatim to `AUDIT_DIR/07-approvals.md`. Nothing is implemented without this file.
3. Run the **baseline benchmark round** (see the 10 entry) before any implementation.
4. Then **08**, in a fresh session.

### Prompt 08: Implement approved P0

**BEFORE**
- `07-harness-design.md`, `07-approvals.md` and the baseline benchmark records exist.
- Git state of affected repositories is understood (clean, or you know why not).
- Session: **FRESH**, from persisted artifacts only. Mutation: **APPROVAL REQUIRED**: the prompt produces a change manifest and waits for you before writing anything.

**RUN:** [08-implement-p0.md](08-implement-p0.md). In your first message give it the approved list (point it to `07-approvals.md`).

**WHILE RUNNING**
- Expect: preflight (Git recoverability, backups outside repositories), the change manifest, a pause for your confirmation, then implementation and validation.
- Do not allow: application source changes, secrets, edits to shared/team-wide/managed configuration you did not approve, commits or pushes, items that were not approved.
- Read the manifest carefully before confirming. This is the one phase that changes things.
- Helpers: Stop Scope Creep, Diagnostic Only (until you confirm the manifest).

**DONE WHEN:** `08-implementation-report.md` lists changed files, the effective hierarchy, validation results (skills discovered, hooks/agents/MCPs checked), conflicts, before/after always-loaded context, and rollback instructions, and Claude has not committed or pushed.

**AFTER**
1. [Phase Completeness Check](helpers/phase-completeness-check.md)
2. [Independent Review Handoff](helpers/independent-review-handoff.md) (writes the neutral review brief)
3. [Freeze Phase](helpers/freeze-or-checkpoint.md) (`NEXT_PROMPT: 09-adversarial-review.md`)
4. Start a **separate, fresh session** for **09**. Do not reuse the implementation session.

### Prompt 09: Adversarial review

**BEFORE:** the implemented configuration, `07-harness-design.md`, `08-implementation-report.md` and `handoff/09-review-brief.md` exist. Session: **FRESH and independent**. Mutation: report only (`09-review.md`).

**RUN:** [09-adversarial-review.md](09-adversarial-review.md), then paste the line the Review Handoff gave you ("Read … 09-review-brief.md first. Treat every claim … as unverified.").

**WHILE RUNNING**
- Expect: file inspection, context-report probes in `WORKSPACE` and in each repository, skill trigger/near-miss tests, and a ranked findings list biased toward removal.
- Do not allow: applying fixes, editing configuration to make a probe pass.
- Helpers: Diagnostic Only (recommended for the whole session), Construct Evaluator, Stop Scope Creep.

**DONE WHEN:** `09-review.md` has its six sections, with findings ranked CRITICAL/HIGH/MEDIUM/LOW, each with evidence, a failure scenario and a recommended action; CRITICAL and HIGH findings were printed.

**AFTER**
1. Completeness Check → Freeze.
2. **You** decide which findings to apply. Record the decision in `AUDIT_DIR/09-decisions.md`.
3. Apply approved fixes using the same discipline as prompt 08 (fresh session, manifest, confirmation, rollback). For deletions and simplifications this may be quick, but do not skip the manifest.
4. Then the **harness benchmark round** (10).

### Prompt 10: Benchmark

Prompt 10 is run **twice**: once for the **baseline** (after 07 approval, before 08) and once for the **harness round** (after 09's fixes).

**BEFORE**
- Baseline round: `07-harness-design.md`, `07-approvals.md`, and the index script from 02. Harness round: fixes from 09 applied.
- Use a scratch branch or worktree (short path on Windows) that you can discard. Never benchmark on a branch holding work you need.
- Session: see the manual flow below. Mutation: scratch worktree only, plus records under `AUDIT_DIR/10-benchmark/`.

**RUN:** [10-benchmark-harness.md](10-benchmark-harness.md)

**Manual flow** (prompt 10 describes Claude driving the runs; operated by hand, you drive them):
1. **Planning session (NEW).** Run prompt 10 through Steps 1–2 only: define the task set and conditions, with expected outcomes and automated verification fixed *before* any run. Freeze it (`MODE: PHASE-COMPLETE`) so the task list is on disk and identical for both rounds.
2. **Each task run (NEW session per run, normal usage).** Reset the scratch starting point, start a fresh session in the normal launch directory, and give the task's exact prompt text with no extra hints and no runbook preamble beyond what you would normally use. Run the task's verification yourself and note PASS/FAIL.
3. **After each run:** [Record Benchmark](helpers/record-benchmark.md), preferably in another new session so the recording cost is not counted.
4. **Analysis session (NEW).** Run prompt 10 Steps 4–5 over the records: cost per successfully verified task, secondary metrics, construct contribution, remaining bottlenecks.

**WHILE RUNNING**
- Expect: noisy single runs (do several if budget allows), tasks that fail.
- Do not allow: tuning the harness between runs of the same round, hints in task prompts, pushing, or touching shared branches.
- Helpers: Record Benchmark, Stop Scope Creep.

**DONE WHEN:** the baseline and harness rounds each have the same task set run under the same settings, every run has a record and a verification result, and `10-benchmark/report.md` contains results with spread, remaining bottlenecks classified as native-solvable or later-phase requirements, and recommendations.

**AFTER:** Freeze. Decide with the data what to keep, simplify or remove. Only if large, repeated, native-unaddressable bottlenecks remain should the later optional code-intelligence phase be considered.

---

## 6. Troubleshooting

- **A new session doesn't know where your files are.** Paste the section 1 preamble; the handoff file also records the launch directory and environment.
- **Claude says it cannot find a report.** Check `AUDIT_DIR` spelling and that the phase was frozen. Run [What Should I Do Next?](helpers/what-next.md).
- **The recovery test keeps failing for the same reason.** The phase prompt's report is probably missing the content; put the missing material into the report (not only the handoff) during Repair. If the cause looks like a flaw in a major prompt, do not edit the prompt: write it up in [PROPOSED-CHANGES.md](PROPOSED-CHANGES.md).
- **A phase went wrong.** Stop Scope Creep, then inspect with read-only commands. Prompt 08 recorded rollback steps for everything it changed; other phases changed nothing outside `AUDIT_DIR`.
- **Windows specifics.** See the README's Windows notes: native vs WSL have separate histories and configuration locations.
