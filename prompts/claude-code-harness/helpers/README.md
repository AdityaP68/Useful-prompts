# Helper Prompts

Short copy/paste prompts for the operational gaps *between* the major phase prompts. They are optional for you but the [runbook](../RUNBOOK.md) tells you exactly when each one applies. They never replace or modify a major prompt.

Open the file on GitHub, copy the block under **Copy/Paste Prompt**, fill in the variables at the top of the block, and paste it into Claude Code.

| Helper | File | Session | Mutation |
|---|---|---|---|
| A + E. Freeze Phase / Pre-Clear Checkpoint | [freeze-or-checkpoint.md](freeze-or-checkpoint.md) | SAME | ARTIFACTS ONLY |
| B. Phase Completeness Check | [phase-completeness-check.md](phase-completeness-check.md) | SAME | NONE |
| C. Fresh Session Recovery Test | [fresh-session-recovery-test.md](fresh-session-recovery-test.md) | NEW | NONE |
| D. Repair Handoff | [repair-handoff.md](repair-handoff.md) | ORIGINAL or NEW | ARTIFACTS ONLY |
| F. Stop Scope Creep, G. Diagnostic Only | [guardrails.md](guardrails.md) | SAME / EITHER | NONE |
| H. Safe Historical Analysis | [safe-historical-analysis.md](safe-historical-analysis.md) | EITHER | ARTIFACTS ONLY |
| I. Construct Evaluator | [construct-evaluator.md](construct-evaluator.md) | EITHER | NONE |
| J. Record Benchmark | [record-benchmark.md](record-benchmark.md) | NEW (or SAME) | ARTIFACTS ONLY |
| K. Independent Review Handoff | [independent-review-handoff.md](independent-review-handoff.md) | SAME | ARTIFACTS ONLY |
| L. What Should I Do Next? | [what-next.md](what-next.md) | EITHER | NONE |

Helpers A and E are one prompt with two modes, and F and G share a file, to keep the manual workflow simple.

## Variables used in every helper

- `AUDIT_DIR`: the folder **outside every repository** where phase reports, handoffs and `STATUS.md` are kept. These files are local working material and may contain real identifiers; never commit them to a shared or public repository.
- `PHASE`, `NEXT_PROMPT`, `TASK_ID` and similar: filled in by you each time, as described in the block.

## Format of every helper

```
# Prompt Name
**When to use:** ...
**Session:** SAME / NEW / EITHER
**Mutation:** NONE / ARTIFACTS ONLY / APPROVAL REQUIRED

## Copy/Paste Prompt
(one fenced text block)
```

"ARTIFACTS ONLY" means Claude may write under `AUDIT_DIR` and nowhere else.
