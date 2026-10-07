# Prompt Lifecycle and Status Tracking

Two separate things are tracked, and they live in different places:

1. **Prompt maturity** (DRAFT / TESTED / PROVEN): a property of the *prompts*, recorded here, in Git.
2. **Run status** (NOT STARTED / IN PROGRESS / COMPLETED / VALIDATED): a property of *your own run*, recorded **locally, outside Git**.

No tooling is needed for either.

---

## 1. Prompt maturity

| Level | Meaning |
|---|---|
| **DRAFT** | Written and reviewed, not yet run end to end against a real workspace. May be edited freely, keeping diffs small and reviewable. |
| **TESTED** | Run successfully at least once against a real workspace; the output was useful. Protected: no casual rewrites. |
| **PROVEN** | Tested, and its output was consumed by the following phase without rework (or it has been run successfully more than once). Protected: changes follow the change process below. |

### Current maturity

| Prompt | Maturity |
|---|---|
| 01 Inventory existing constructs | TESTED |
| 02 – 10 major prompts | DRAFT |
| Helpers | DRAFT |
| RUNBOOK | DRAFT |

Maturity labels are recorded only in this table, **not inside the prompt files**, so promoting a prompt never changes its text. Promote by editing this table once the criteria above are met (for example 01 becomes PROVEN once phase 02 has successfully consumed its output). Execution details of any private run are intentionally not recorded in this repository.

### Changing a TESTED or PROVEN prompt

A prompt that has been successfully exercised is not rewritten for style, consistency or minor polish. When there is a real reason to change it:

1. **Identify the observed problem.** What actually went wrong or cost too much, in a real run? Note it generically.
2. **Propose the change** in [PROPOSED-CHANGES.md](PROPOSED-CHANGES.md): `PROPOSED CHANGE`, `WHY`, `COMPATIBILITY IMPACT`.
3. **Preserve intent.** Keep the semantics, the required output sections and the file names later steps depend on (see "Interfaces" below). Prefer an additive edit to a rewrite.
4. **Review the diff.** Read it line by line; reject anything that is not the proposed change.
5. **Test the revised version** in a real run, comparing the output against the previous one.
6. **Promote.** Update this table and remove the entry from the proposals list (Git history records the rest).

**Exception:** a change that is necessary for **safety** (for example, a prompt that could expose secrets or mutate something it should not) may be made immediately. Record it in [PROPOSED-CHANGES.md](PROPOSED-CHANGES.md) after the fact with the reason.

**Do not duplicate versions.** No `v2` copies or parallel files; Git history is the version history. If you need to point to a specific proven state, tag it in Git.

### Interfaces that later steps depend on

Treat these as a contract; changing them is a compatibility impact.

- Report file names: `01-inventory.md`, `02-history-findings.md`, `03-hierarchy.md`, `04-skill-candidates.md`, `05-subagent-candidates.md`, `06-hooks-and-mcps.md`, `07-harness-design.md`, `08-implementation-report.md`, `09-review.md`, `10-benchmark/report.md`, all under `AUDIT_DIR`.
- Their required section lists, as specified in each prompt's "Output" section.
- Helper-created files: `handoff/<NN>-handoff.md`, `handoff/09-review-brief.md`, `07-approvals.md`, `09-decisions.md`, `STATUS.md`, `10-benchmark/runs/`, `10-benchmark/runs-index.md`.
- Placeholder names: `AUDIT_DIR`, `WORKSPACE`, `REPOSITORY` and the others used throughout.

---

## 2. Run status (local, not in Git)

Track your own progress in `AUDIT_DIR/STATUS.md`. `AUDIT_DIR` is outside every repository and must never be committed or shared, because it holds your real findings.

| Status | Meaning |
|---|---|
| **NOT STARTED** | The phase has not been run. |
| **IN PROGRESS** | Running, or checkpointed mid-phase. |
| **COMPLETED** | The phase finished, the Completeness Check returned COMPLETE, and the report and handoff are frozen on disk. |
| **VALIDATED** | A Fresh Session Recovery Test returned SUFFICIENT for 01–07 (for 08: review 09 found no unresolved CRITICAL/HIGH items; for 09: you triaged every finding; for 10: results were reviewed and the benchmark can be re-run). Only a human sets this. |

### Local status file

Create `AUDIT_DIR/STATUS.md` like this (replace nothing in the repository, this is a local file):

```markdown
# Harness run status

| Phase | Status | Artifacts | Recovery test | Notes |
|---|---|---|---|---|
| 01 Inventory | NOT STARTED | | | |
| 02 History mining | NOT STARTED | | | |
| 03 Hierarchy | NOT STARTED | | | |
| 04 Skills | NOT STARTED | | | |
| 05 Subagents | NOT STARTED | | | |
| 06 Hooks and MCPs | NOT STARTED | | | |
| 07 Harness design | NOT STARTED | | | approvals: not recorded |
| 10 Baseline round | NOT STARTED | | | |
| 08 Implement P0 | NOT STARTED | | | |
| 09 Review | NOT STARTED | | | |
| 10 Harness round | NOT STARTED | | | |
```

The Freeze helper updates the Status and Notes columns to COMPLETED / IN PROGRESS; you set VALIDATED after the recovery test. If `STATUS.md` and the files in `AUDIT_DIR` disagree, trust the files; the [What Should I Do Next?](helpers/what-next.md) helper checks this.
