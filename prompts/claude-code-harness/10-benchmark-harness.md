# 10 — Benchmark the Harness (Before vs After)

**Mode:** measurement. Runs real tasks; may modify a *scratch* branch/worktree only. **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/02-history-findings.md` (baseline ideas and the index script), `<AUDIT_DIR>/07-harness-design.md`, `<AUDIT_DIR>/09-review.md`. **Writes:** `<AUDIT_DIR>/10-benchmark/` (task definitions, raw results, report).

---

HARNESS WORKFLOW  
Prompt ID: H10  
Phase: Harness Benchmark  
Previous Major Phase: H07 — Native Harness Synthesis (baseline round) or H09 — Adversarial Review (harness round)  
Next Major Phase: NONE (a human decides what to keep, simplify or remove)  
Primary artifact: <AUDIT_DIR>/10-benchmark/report.md  
This prompt is used twice: a baseline round before H08, and a harness round after H09's fixes.  

This prompt was manually copied from an external prompt library.  
Do not assume that library or its filenames are available in this environment.  
The Prompt ID is a logical workflow identifier, not a local file path.  
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the  
harness artifact root (a folder outside every repository). Earlier phases, if any,  
left their results there; inspect those artifacts, not any external files.  
When this phase is finished, end your final message with:  
"H10 complete. Recommended next operation: HCHECK - Phase Completeness Check."  

---

Design and run a before/after comparison of the harness on **representative real engineering tasks**. Run it once as a baseline (before prompt 08, or with the new configuration disabled) and once after the harness is implemented and reviewed. This prompt can be used repeatedly as the harness evolves.

## Primary metric

**Cost per successfully verified engineering task** = total cost of all attempts on a task ÷ number of tasks that pass the pre-defined verification. A failed or unverified task contributes its cost to the numerator and nothing to the denominator.

## Secondary metrics

- repeated repository discovery (how often the same orientation facts are rediscovered)
- unnecessary tool calls
- time to first correct plan
- context retrieval efficiency (relevant files read ÷ files read; relevant tokens ÷ tokens retrieved)
- correctness/regression rate

## Measurements (record where available; mark unavailable ones as N/A, do not invent them)

input tokens · output tokens · cached tokens (read and creation) · cost · task duration · Read calls · Grep/search calls · Glob calls · Bash calls · MCP calls · subagent calls · Skill invocations · distinct files inspected · repeated reads · context volume (peak and total) · test executions · failed attempts · incorrect assumptions · regressions introduced · task completion · verification success

## Rules

- Never run benchmark tasks on a branch or working tree containing work that must be preserved. Use a scratch branch or a separate worktree created for this purpose, and clean it up afterwards. Never push, never commit to shared branches, never alter shared configuration.
- Do not include secrets in task definitions or results. Do not publish raw results without sanitizing identifiers.
- Do not use synthetic microbenchmarks as the main evidence. Use tasks drawn from real work patterns found in `02-history-findings.md` (or recent real tickets I provide), expressed with placeholders in the shared description and the real details kept private.
- Check the current Claude Code documentation for how to obtain per-session token, cost and tool-call data (session records, built-in usage reporting, or non-interactive/headless modes with structured output). Use what exists in this version. Reuse the index script from prompt 02 where it applies.
- Control variance: same model, same settings, same starting commit, fresh session per run; several runs per task per condition if budget allows, since single runs are noisy. State the number of runs and report the spread, not only means.

## Step 1 — Define the task set

Select 6–12 tasks covering the workflow mix found in history, for example (generic categories; supply real instances privately):

- single-repository bug fix with a failing test
- single-repository feature or refactor
- cross-repository change (contract, shared library, or dependency update across `<REPOSITORY>` instances)
- investigation question about behaviour, an API or an event, with a checkable answer
- debugging from a log or stack trace
- dependency/library version upgrade
- test/verification-only task
- documentation or knowledge update
- a task each for each language that matters in the workspace (Java, JavaScript/TypeScript, Python, YAML/configuration), where relevant

For each task define, **before running anything**:

| Field | Content |
|---|---|
| ID and category | |
| Starting point | Commit/branch for each affected `<REPOSITORY>` |
| Prompt | The exact instruction given to Claude |
| Expected outcome | What a correct result looks like |
| Verification | Automated checks (tests, build, type check, lint, contract check) and, where needed, a rubric for human review |
| Time/cost cap | Abort threshold |
| Notes on hidden traps | Known pitfalls, so incorrect assumptions can be counted |

## Step 2 — Define the conditions

- **Baseline**: the pre-harness configuration (or the harness disabled via supported mechanisms, with the exact method recorded).
- **Harness**: the implemented configuration.
- Optional ablations: the harness with one construct removed (for example, with Skills only or with the MCP surface reduced) to see which constructs contribute.

Confirm that the only difference between conditions is the configuration under test.

## Step 3 — Execute

For each task × condition × run: reset the starting point, start a fresh session in the right directory, give the prompt, let it work without extra hints, capture the metrics, run the verification, and record the outcome. Record any manual interventions or user corrections as events. Do not tune the harness between runs of the same benchmark round.

## Step 4 — Analyze

- per-task and aggregate **cost per successfully verified task**, with run-to-run spread
- completion and verification rates; regressions introduced
- secondary metrics, before vs after
- which constructs fired (Skills, agents, hooks, MCP) and whether they helped
- remaining waste: tasks where discovery or navigation still dominated cost
- tasks where the harness made things worse, and why
- caveats: sample size, model variance, task selection bias, cache effects

## Step 5 — Decide

Produce recommendations in three buckets: **keep**, **simplify or remove**, and **bottlenecks that remain**. For each remaining bottleneck, state whether a native construct could address it or whether it matches a requirement recorded in prompt 07 for a later custom-intelligence phase. Only if a bottleneck is large, repeated and unaddressable natively should that phase be considered.

## Output

Write `<AUDIT_DIR>/10-benchmark/report.md` (plus `tasks.md` and raw per-run records):

1. Method and conditions, including exact settings and version information
2. Task definitions (sanitized)
3. Results tables: primary metric, secondary metrics, per task and aggregate, with spread
4. Construct contribution analysis
5. Failures and regressions
6. Remaining bottlenecks and classification (native-solvable vs later-phase requirement)
7. Recommendations (keep / simplify / remove)
8. How to re-run this benchmark next time

Print a summary (at most 40 lines) in chat. Clean up scratch branches/worktrees you created and say what you removed.
