# 09 — Adversarial Review of the Harness

**Mode:** read-only. **Run from:** `<WORKSPACE>` (ideally a fresh session, so the reviewer does not share the implementer's assumptions).
**Reads:** the implemented configuration, `<AUDIT_DIR>/07-harness-design.md`, `<AUDIT_DIR>/08-implementation-report.md`. **Writes:** `<AUDIT_DIR>/09-review.md` only.

---

HARNESS WORKFLOW  
Prompt ID: H09  
Phase: Adversarial Review  
Previous Major Phase: H08 — P0 Implementation  
Next Major Phase: Human decision on fixes, then H10 — Harness Benchmark (harness round)  
Primary artifact: <AUDIT_DIR>/09-review.md  

This prompt was manually copied from an external prompt library.  
Do not assume that library or its filenames are available in this environment.  
The Prompt ID is a logical workflow identifier, not a local file path.  
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the  
harness artifact root (a folder outside every repository). Earlier phases, if any,  
left their results there; inspect those artifacts, not any external files.  
When this phase is finished, end your final message with:  
"H09 complete. Recommended next operation: HCHECK - Phase Completeness Check."  

---

You are a skeptical reviewer. Your job is to find reasons the harness will cost more, mislead Claude, break for other developers, or rot, and to recommend **removing or simplifying** it. Assume nothing is justified until you have seen evidence.

## Rules

- Do NOT modify anything except the report.
- Review the *actual files and behaviour*, not the design's claims. Verify with the product's diagnostics where available and by reading the files.
- Re-check the current Claude Code documentation for loading, precedence and trigger behaviour before judging.
- **Prefer removing or simplifying constructs over adding new ones.** Every "add" recommendation must show why no removal can solve the problem.
- Findings need evidence (file, line or measurement) and a concrete failure scenario, not stylistic preference.

## Review checklist

1. **Unnecessary instructions**: lines Claude would follow anyway, facts discoverable from code, anything not tied to an observed failure.
2. **Duplicated knowledge**: same fact in several places, between levels or between instructions, Skills and docs.
3. **Conflicts**: contradictory instructions across user/workspace/repository; precedence surprises.
4. **Overlapping Skill triggers**: descriptions that could fire for the same request; descriptions too broad or too vague; Skills that never fire.
5. **Oversized Skills**: bodies that should be split into on-demand references; reference files that are always loaded in practice.
6. **Skill/script confusion**: deterministic work left to the model; scripts that embed judgement.
7. **Unnecessary agents**: agents without isolation, compact-return or parallelism justification; agents whose briefing cost exceeds the savings.
8. **Expensive hooks**: slow, high-frequency, noisy, blocking-without-need, silent on failure, non-portable.
9. **Excessive MCP surface**: unused tools, large definitions, large responses, duplicated capabilities.
10. **Scope mistakes**: personal content in shared files, workspace-specific content inside repositories, repository knowledge stranded in the workspace.
11. **Stale knowledge**: paths, commands, versions or behaviours that no longer match the code. Check them.
12. **Repository portability**: does each repository still work for someone who clones only it, with no workspace configuration, on another OS?
13. **Workspace/repository duplication**: identical or near-identical text at two levels.
14. **Net token increase**: constructs that increase always-loaded context or tool-call counts more than they save. Compute before/after.
15. **Safety**: broad permissions, secrets in configuration, hooks with side effects, data leaving the machine.
16. **Maintainability**: who updates each item, what detects staleness, whether the knowledge lifecycle is realistic.
17. **Verification gaps**: places where a cost reduction could reduce correctness without detection.

## Method

- Run at least these probes using real prompts and the product's context reporting where available: a trivial question in `<WORKSPACE>`; a single-repository task started in `<REPOSITORY>`; a cross-repository task from `<WORKSPACE>`; a request near each new Skill's boundary. Record what loaded and what Claude did.
- For each Skill, test one prompt that should trigger it and one that should not.
- Look for the simplest change that removes each problem.

## Output

Write `<AUDIT_DIR>/09-review.md`:

1. Verdict summary (is the harness net-positive? by how much, with what uncertainty)
2. Findings, ranked **CRITICAL / HIGH / MEDIUM / LOW**, each with:
   - ID, title, severity
   - evidence (file/line/measurement)
   - failure scenario
   - recommended action (prefer REMOVE or SIMPLIFY) and expected effect on cost/correctness
3. Constructs recommended for removal or merging
4. Things that are working and should be left alone
5. Probe results
6. Open questions

Print the summary and the CRITICAL/HIGH findings in chat. Do not apply fixes; I will decide which to apply (via prompt 08 conventions) before benchmarking.

- **CRITICAL**: causes incorrect results, leaks secrets, breaks repositories for other developers, or substantially increases cost.
- **HIGH**: likely to cause recurring waste or errors.
- **MEDIUM**: maintainability or moderate waste.
- **LOW**: polish.
