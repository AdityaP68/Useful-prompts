# Proposed Changes to Major Prompts

Changes to existing major prompts are **proposed here first**, not applied silently (see [LIFECYCLE.md](LIFECYCLE.md#changing-a-tested-or-proven-prompt)). Nothing below has been applied. None of them is necessary for safety; the [runbook](RUNBOOK.md) already works around each one.

Remove an entry once its change has been tested and promoted, or if it is rejected.

---

## PC-1: Prompt 07 should persist the approval decisions

**Prompt:** [07-synthesize-native-harness.md](07-synthesize-native-harness.md)

**PROPOSED CHANGE:** At the end of 07, after asking which P0 items are approved, have Claude write the answers to `AUDIT_DIR/07-approvals.md`.

**WHY:** Prompt 08 implements "only approved P0", but no prompt defines where the approved list is stored. By hand this is a copy/paste step that is easy to lose across sessions.

**COMPATIBILITY IMPACT:** Additive; adds one output file. No existing section or file name changes. Current workaround: the runbook has you record the decisions in `07-approvals.md` after the approval gate.

## PC-2: Prompt 10 should say who runs the benchmark tasks

**Prompt:** [10-benchmark-harness.md](10-benchmark-harness.md)

**PROPOSED CHANGE:** In Step 3 ("Execute"), add a note that when the benchmark is operated manually, each task run is started by the operator in its own fresh session, with the Record Benchmark helper capturing the metrics.

**WHY:** The prompt reads as if a single Claude session executes every run and measures itself, which conflicts with the goal of measuring normal fresh-session usage and with not counting measurement overhead.

**COMPATIBILITY IMPACT:** Documentation-only. No output files or sections change. Current workaround: the runbook's "Manual flow" under Prompt 10.

## Resolved

- **Binding `AUDIT_DIR` and resolving cross-references in a fresh session** (formerly PC-3). Resolved without changing any prompt body: the metadata header now inserted at the top of every major prompt tells private Claude that `AUDIT_DIR` is the harness artifact root, that "prompt NN" means phase `HNN`, and that no external library is available. See [PROTOCOL.md](PROTOCOL.md).
