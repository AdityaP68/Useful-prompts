# Prompt Catalog (control panel)

When Claude in your private environment says **"run HCHECK"**, find `HCHECK` below, tap the file, copy it, paste it. That is the whole workflow.

IDs are **logical labels**, not file paths. The pasted prompt tells Claude what it is; the private environment never needs this repository. Details: [PROTOCOL.md](PROTOCOL.md). Step-by-step operation: [RUNBOOK.md](RUNBOOK.md).

## All prompts

| ID | Type | Name | GitHub file | Normally follows | Session |
|---|---|---|---|---|---|
| H01 | Major | Inventory Existing Constructs | [01-inventory-existing-constructs.md](01-inventory-existing-constructs.md) | start | NEW |
| H02 | Major | Historical Usage Mining | [02-mine-historical-usage.md](02-mine-historical-usage.md) | H01 + HRECOVER | NEW |
| H03 | Major | Context Hierarchy Design | [03-design-context-hierarchy.md](03-design-context-hierarchy.md) | H02 + HRECOVER | NEW |
| H04 | Major | Skill Discovery | [04-discover-skills.md](04-discover-skills.md) | H03 | NEW (or SAME if small) |
| H05 | Major | Subagent Discovery | [05-discover-subagents.md](05-discover-subagents.md) | H04 | NEW (or SAME if small) |
| H06 | Major | Hooks and MCP Audit | [06-audit-hooks-and-mcps.md](06-audit-hooks-and-mcps.md) | H05 | NEW (or SAME if small) |
| H07 | Major | Native Harness Synthesis | [07-synthesize-native-harness.md](07-synthesize-native-harness.md) | H06 + HRECOVER | NEW |
| H08 | Major | P0 Implementation | [08-implement-p0.md](08-implement-p0.md) | human approval + H10 baseline | NEW |
| H09 | Major | Adversarial Review | [09-adversarial-review.md](09-adversarial-review.md) | H08 + HREVIEW | NEW, independent |
| H10 | Major | Harness Benchmark | [10-benchmark-harness.md](10-benchmark-harness.md) | H07 approval (baseline) / H09 fixes (harness round) | NEW per run |
| HCHECK | Helper | Phase Completeness Check | [phase-completeness-check.md](helpers/phase-completeness-check.md) | any finished major phase | SAME |
| HFREEZE | Helper | Freeze Current Phase | [freeze-phase.md](helpers/freeze-phase.md) | HCHECK says COMPLETE | SAME |
| HCHECKPOINT | Helper | Pre-Clear / Pre-Compaction Checkpoint | [checkpoint.md](helpers/checkpoint.md) | mid-phase, before clearing | SAME |
| HRECOVER | Helper | Fresh Session Recovery Test | [fresh-session-recovery-test.md](helpers/fresh-session-recovery-test.md) | HFREEZE | NEW |
| HREPAIR | Helper | Repair Handoff | [repair-handoff.md](helpers/repair-handoff.md) | HRECOVER says INSUFFICIENT | original if open, else NEW |
| HSCOPE | Helper | Scope Guard | [scope-guard.md](helpers/scope-guard.md) | any time Claude exceeds scope | SAME |
| HDIAG | Helper | Diagnostic Only | [diagnostic-only.md](helpers/diagnostic-only.md) | any time | EITHER |
| HHISTORY | Helper | Safe History Analysis | [safe-historical-analysis.md](helpers/safe-historical-analysis.md) | with H02 | EITHER |
| HEVAL | Helper | Construct Evaluator | [construct-evaluator.md](helpers/construct-evaluator.md) | during H04, H05, H06, H09 | EITHER |
| HBENCH | Helper | Record Benchmark | [record-benchmark.md](helpers/record-benchmark.md) | after each H10 run (or a recovery test) | NEW (or SAME) |
| HREVIEW | Helper | Independent Review Handoff | [independent-review-handoff.md](helpers/independent-review-handoff.md) | HCHECK on H08 | SAME |
| HNEXT | Helper | What Should I Do Next | [what-next.md](helpers/what-next.md) | any time | EITHER |

"Normally follows" is orientation only. Private Claude's HNEXT answer is the authority for where you are.

## Workflow map

```
H01  Inventory Existing Constructs
 ↓
HCHECK
 ↓
HFREEZE
 ↓
[new session if the conversation is large]
 ↓
HRECOVER ──INSUFFICIENT──► HREPAIR ──► (new session) HRECOVER
 ↓ SUFFICIENT
HBENCH  (optional: record what the recovery cost)
 ↓
H02  Historical Usage Mining          (HHISTORY alongside)
 ↓
HCHECK → HFREEZE → [new session] → HRECOVER
 ↓
H03 → HCHECK → HFREEZE → ...
 ↓
H04 → ... → H05 → ... → H06 → ... (HEVAL for borderline candidates)
 ↓
HCHECK → HFREEZE → [new session] → HRECOVER
 ↓
H07  Native Harness Synthesis
 ↓
HCHECK → HFREEZE
 ↓
HUMAN APPROVAL of P0 items
 ↓
H10  Benchmark, baseline round   (HBENCH after each run)
 ↓
H08  P0 Implementation           (fresh session; confirm the change manifest)
 ↓
HCHECK → HREVIEW → HFREEZE
 ↓
H09  Adversarial Review          (separate, independent fresh session)
 ↓
HUMAN DECISION on fixes → apply them (H08 discipline)
 ↓
H10  Benchmark, harness round    (HBENCH after each run)

Any time:  HNEXT (where am I?)   HSCOPE (Claude is out of scope)   HDIAG (no changes)
           HCHECKPOINT (session getting heavy mid-phase)
```

## What each major phase produces privately

The public file (above) is for you. The private artifact is what Claude writes in your environment, relative to its artifact root (`AUDIT_DIR` in the prompts).

| ID | Private output artifact | Then run |
|---|---|---|
| H01 | `01-inventory.md` | HCHECK → HFREEZE → HRECOVER |
| H02 | `02-history-findings.md` (+ analysis script) | HCHECK → HFREEZE → HRECOVER |
| H03 | `03-hierarchy.md` | HCHECK → HFREEZE |
| H04 | `04-skill-candidates.md` | HCHECK → HFREEZE |
| H05 | `05-subagent-candidates.md` | HCHECK → HFREEZE |
| H06 | `06-hooks-and-mcps.md` | HCHECK → HFREEZE → HRECOVER |
| H07 | `07-harness-design.md` (+ your `07-approvals.md`) | HCHECK → HFREEZE → human approval |
| H08 | `08-implementation-report.md` | HCHECK → HREVIEW → HFREEZE |
| H09 | `09-review.md` | HCHECK → HFREEZE → human decision |
| H10 | `10-benchmark/report.md`, `10-benchmark/runs/` | HBENCH per run, then HFREEZE |

Helpers also create `STATE.md` (or use an equivalent state file) and `handoff/` files in the same private root. You do not create them by hand.

## If Claude cannot tell which phase you mean

Normally you paste a helper unchanged: it auto-detects the phase from the private artifacts and asks you only if it cannot. When Claude says it cannot determine the phase, paste this with the helper, edited to match:

```text
HARNESS CONTEXT
Current/target phase: H01 — Inventory Existing Constructs
Operation: Phase Completeness Check

This is metadata only.
The public prompt repository is not available in this environment.
Use persistent local harness artifacts as the source of execution state.
```

## Naming scheme

- `H01`–`H10`: major phases. `H` + two digits.
- `HCHECK`, `HFREEZE`, …: helpers. `H` + an uppercase word.
- IDs are never reused or renumbered; files can be renamed (update the table). See [PROTOCOL.md](PROTOCOL.md#2-id-scheme).
