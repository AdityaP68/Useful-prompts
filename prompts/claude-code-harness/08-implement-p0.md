# 08 — Implement the Approved P0 Configuration

**Mode:** WRITES configuration. **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/07-harness-design.md` and the list of P0 items I approve. **Writes:** only the files listed in the approved change manifest, plus `<AUDIT_DIR>/08-implementation-report.md`.

---

Implement **only** the P0 items that I have explicitly approved. Be conservative. If I have not given you an approved list, stop and ask for it.

## Hard rules

- Implement ONLY approved P0 items. Do not add P1/P2 items, "while I'm here" improvements, or extra constructs.
- Do NOT modify application source code, build files, dependency manifests, tests, CI configuration or generated files.
- Do NOT write secrets, tokens or credentials into any file. Do not copy environment values into configuration.
- Do NOT change shared, team-wide, company-wide or managed configuration, and do NOT edit files in repositories that others consume, unless I have approved that exact file. Prefer personal or local scopes for experiments.
- Do NOT delete user content. Where something is superseded, move it to an archive location or leave it in place and flag it for me to remove, as the approved item specifies.
- Re-check the current Claude Code documentation for file formats and locations before writing anything. Do not rely on memory.
- Stop and ask if the environment differs from what the design assumed (for example a path no longer exists, a feature is unavailable in this version, or the repository has uncommitted changes in a file you need to touch).

## Step 1 — Preflight

1. Restate the approved P0 items and any I excluded.
2. Verify Git recoverability for every location to be changed:
   - for each affected repository, report whether the working tree is clean; do not commit or stash my work
   - for configuration outside any repository (user scope, workspace directory that is not a repository), create a timestamped backup copy under `<AUDIT_DIR>/backup/` of every file you will modify, before modifying it
3. Confirm that `<AUDIT_DIR>` is outside every repository.
4. Record the baseline: always-loaded context for a session started in `<WORKSPACE>` and in each affected `<REPOSITORY>` (use the product's context reporting where available, otherwise a labelled estimate).

## Step 2 — Change manifest (and pause)

Produce a manifest and show it to me **before modifying anything**:

| File (placeholder-relative) | Create / Modify / Move | Scope | Shared or personal | Purpose | Size estimate | Backup/recovery method |
|---|---|---|---|---|---|---|

Include any commands you intend to run that have side effects. Wait for my confirmation of the manifest. Only then proceed.

## Step 3 — Implement

- Create only manifest files, with the minimum content specified in the design.
- For approved Skills, use the installed Skill Creator capabilities if available (draft, test with the planned trigger/near-miss prompts, tune the description). If it is not available, follow the current documentation's Skill format and say so.
- Keep every instruction short and non-duplicated. Reference documentation by path rather than copying it.
- Keep scripts cross-platform (or provide a note about which OS they support), deterministic, free of secrets, and with clear failure output.
- Hooks: cheap, non-blocking unless the design says otherwise, with explicit timeouts and a safe failure mode.
- Agents and MCP changes: exactly as approved, with minimal tools.

## Step 4 — Validate

- Run the product's diagnostics/listing commands (as supported by this version) to confirm that: Skills are discovered with the expected descriptions; agents load; hooks parse and fire as expected (test with a harmless trigger); MCP servers start and expose only the expected tools; settings files are valid.
- Start a fresh session (or use the supported reload mechanism) in `<WORKSPACE>` and in each affected `<REPOSITORY>` independently, and confirm the effective instructions are as designed. Confirm that a repository still works on its own.
- Test each new Skill with its trigger and near-miss prompts.
- Look for conflicts: duplicate or contradictory instructions across levels, overlapping Skill descriptions, hooks that duplicate permissions or rules.
- Re-measure always-loaded context and compare with the baseline.

## Step 5 — Report

Write `<AUDIT_DIR>/08-implementation-report.md` and print a concise version:

1. Files created/modified/moved (list)
2. The effective hierarchy as it now resolves (what loads where and when)
3. Validation results for Skills, agents, hooks and MCP servers (pass/fail with brief evidence)
4. Conflicts found and how they were resolved or flagged
5. Always-loaded context before vs after (labelled measured or estimated)
6. Anything not implemented and why
7. **Rollback instructions**: exact steps to restore the previous state for each changed file (Git commands for repository files; restore-from-backup steps for others), and how to confirm the rollback worked
8. Follow-up items for me

Do not commit or push anything unless I ask you to.
