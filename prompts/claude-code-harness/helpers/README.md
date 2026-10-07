# Helper Prompts

Short copy/paste prompts for the operational gaps *between* the major phase prompts. The master list, with links and the workflow map, is [PROMPT-CATALOG.md](../PROMPT-CATALOG.md); the [runbook](../RUNBOOK.md) says when each applies.

Open a file here, copy the block under **Copy/Paste Prompt**, and paste it into Claude Code. Most helpers need **no editing**: they identify themselves, detect the current phase from the private harness artifacts, and ask you only if detection fails.

| ID | Name | File | Session | Mutation |
|---|---|---|---|---|
| HCHECK | Phase Completeness Check | [phase-completeness-check.md](phase-completeness-check.md) | SAME | ARTIFACTS ONLY (state file) |
| HFREEZE | Freeze Current Phase | [freeze-phase.md](freeze-phase.md) | SAME | ARTIFACTS ONLY |
| HCHECKPOINT | Pre-Clear / Pre-Compaction Checkpoint | [checkpoint.md](checkpoint.md) | SAME | ARTIFACTS ONLY |
| HRECOVER | Fresh Session Recovery Test | [fresh-session-recovery-test.md](fresh-session-recovery-test.md) | NEW | ARTIFACTS ONLY (verdict) |
| HREPAIR | Repair Handoff | [repair-handoff.md](repair-handoff.md) | original or NEW | ARTIFACTS ONLY |
| HSCOPE | Scope Guard | [scope-guard.md](scope-guard.md) | SAME | NONE |
| HDIAG | Diagnostic Only | [diagnostic-only.md](diagnostic-only.md) | EITHER | NONE |
| HHISTORY | Safe History Analysis | [safe-historical-analysis.md](safe-historical-analysis.md) | EITHER | ARTIFACTS ONLY |
| HEVAL | Construct Evaluator | [construct-evaluator.md](construct-evaluator.md) | EITHER | NONE |
| HBENCH | Record Benchmark | [record-benchmark.md](record-benchmark.md) | NEW (or SAME) | ARTIFACTS ONLY |
| HREVIEW | Independent Review Handoff | [independent-review-handoff.md](independent-review-handoff.md) | SAME | ARTIFACTS ONLY |
| HNEXT | What Should I Do Next | [what-next.md](what-next.md) | EITHER | NONE |

## Conventions

- **The ID is a logical label, not a file path.** Each pasted helper begins with a short header (`HARNESS WORKFLOW`, helper ID, operation) so private Claude knows what it is without access to this repository. See [PROTOCOL.md](../PROTOCOL.md).
- **Auto-detect first, ask second.** Helpers that act on "the current phase" find it from the private artifacts and workflow-state file. If Claude says it cannot, paste the optional `HARNESS CONTEXT` block from [PROMPT-CATALOG.md](../PROMPT-CATALOG.md#if-claude-cannot-tell-which-phase-you-mean).
- **Private artifacts, not public files.** Helpers refer to the private artifact root (`AUDIT_DIR` in the major prompts), the workflow-state file (`STATE.md` or an existing equivalent) and `handoff/` files, never to files in this repository.
- **Mutation levels.** "ARTIFACTS ONLY" means Claude may write under the private artifact root and nowhere else.
- **Format of every helper:** title with ID, then **When to use / Session / Mutation / Placeholders to fill**, then one fenced `text` block.

The phase table embedded in the helpers is kept in sync by hand; see the maintenance checklist in [PROTOCOL.md](../PROTOCOL.md#6-maintaining-the-protocol).
