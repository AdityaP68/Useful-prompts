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
| H01 Inventory Existing Constructs | TESTED |
| H02 – H10 major prompts | DRAFT |
| Helpers (HCHECK … HNEXT) | DRAFT |
| RUNBOOK, PROMPT-CATALOG, PROTOCOL | DRAFT |

Maturity labels are recorded only in this table, **not inside the prompt files**, so promoting a prompt never changes its text. Promote by editing this table once the criteria above are met (for example H01 becomes PROVEN once H02 has successfully consumed its output). Execution details of any private run are intentionally not recorded in this repository.

**Note on the self-identification header.** Every major prompt, H01 included, carries a short `HARNESS WORKFLOW` metadata header (prompt ID, phase name, previous/next phase, primary artifact, and a note that the prompt was copied from an external library). It was added for portability between the public library and a separate private environment (see [PROTOCOL.md](PROTOCOL.md)). It is metadata only: no instruction in the prompt bodies was changed, and removing the inserted block restores each file exactly. It does not reset maturity.

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

- **Logical IDs** (`H01`–`H10`, `HCHECK` … `HNEXT`): never reused, never renumbered. File names may change; only the catalog row does.
- Private report names: `01-inventory.md`, `02-history-findings.md`, `03-hierarchy.md`, `04-skill-candidates.md`, `05-subagent-candidates.md`, `06-hooks-and-mcps.md`, `07-harness-design.md`, `08-implementation-report.md`, `09-review.md`, `10-benchmark/report.md`, all under `AUDIT_DIR`.
- Their required section lists, as specified in each prompt's "Output" section and mirrored in HCHECK's expected-contents list.
- Helper-created private files: the workflow-state file (`STATE.md` or an existing equivalent), `handoff/<NN>-handoff.md`, `handoff/<NN>-checkpoint.md`, `handoff/<NN>-recovery-gaps.md`, `handoff/09-review-brief.md`, `07-approvals.md`, `09-decisions.md`, `10-benchmark/runs/`, `10-benchmark/runs-index.md`.
- The embedded phase table, headers and workflow-state fields in [PROTOCOL.md](PROTOCOL.md), which are copied into helpers.
- Placeholder names: `AUDIT_DIR`, `WORKSPACE`, `REPOSITORY` and the others used throughout.

---

## 2. Run status (local, not in Git)

Your own progress is tracked in the **private** artifact root (`AUDIT_DIR`), never in this repository. `AUDIT_DIR` is outside every repository and must never be committed or shared, because it holds your real findings.

| Status | Meaning |
|---|---|
| **NOT STARTED** | The phase has not been run. |
| **IN PROGRESS** | Running, or checkpointed mid-phase (HCHECKPOINT). |
| **COMPLETED** | The phase finished, HCHECK returned COMPLETE, and the artifact, handoff and workflow state are frozen on disk (HFREEZE). |
| **VALIDATED** | A fresh-session recovery test (HRECOVER) returned SUFFICIENT. For H08, read it as "H09 found no unresolved CRITICAL/HIGH items"; for H09, "you triaged every finding"; for H10, "results were reviewed and the benchmark can be re-run". |

### The workflow-state file (a contract, not a repository file)

The private environment keeps **one** tiny workflow-state file so any new session can recover the position without this repository and without the old conversation. Helpers create `STATE.md` if nothing equivalent exists, and **detect and use an existing equivalent** (for example a session log that already records harness phases) instead of creating a competing file. The exact fields are defined in [PROTOCOL.md](PROTOCOL.md#5-workflow-state-contract):

```text
Current major phase / Last completed major phase / Status / Last operation /
Next operation / Next major phase / Next session / Primary artifact / Handoff /
Blocking items / Last updated
```

It records operational state only: it is not a knowledge base, a conversation summary or a history. HCHECK, HFREEZE, HCHECKPOINT, HRECOVER, HREPAIR and HREVIEW update it; HNEXT only reads it. You do not create it by hand. If it disagrees with the artifacts that actually exist, the artifacts win, and HNEXT reports the disagreement.
