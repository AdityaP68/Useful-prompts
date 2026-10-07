# Record Benchmark Run

**When to use:** After each individual benchmark task run (see major prompt 10 and the benchmark section of the runbook), to capture its metrics in a consistent, comparable record.
**Session:** NEW is recommended (so the recording overhead is not counted in the measured session): point it at the finished session. SAME also works if you tell it to report totals as of just before this prompt.
**Mutation:** ARTIFACTS ONLY (writes under `AUDIT_DIR/10-benchmark/`)

## Copy/Paste Prompt

```text
AUDIT_DIR: <path outside every repository>
TASK_ID: <short id from the benchmark task list>
CONDITION: <BASELINE | HARNESS | ABLATION: what was removed>
RUN_NUMBER: <1, 2, 3 ...>
MEASURED_SESSION: <how to find it: session id, start time, or "this session">

Record one benchmark run. Do not run, repeat or fix the task. Do not modify any
repository or configuration. Write only under AUDIT_DIR/10-benchmark/.

1. Locate the measured session's record using the history locations documented for
   this Claude Code version (and the product's usage reporting if the session is
   still open). Do not print transcript content.
2. Extract, where available, and write N/A (never an estimate presented as a
   measurement) where not: input tokens, output tokens, cache read tokens, cache
   creation tokens, cost, duration, Read calls, Grep/search calls, Glob calls, Bash
   calls, MCP calls, subagent calls, skill invocations, distinct files inspected,
   repeated reads, peak and total context volume, test executions, failed attempts.
   If this recording session is the measured session, report totals as of just before
   this prompt.
3. Ask me for what cannot be measured automatically and record my answers verbatim:
   did the result pass the task's pre-defined verification (PASS/FAIL), incorrect
   assumptions observed, regressions introduced, user corrections or interventions,
   and anything unusual (interruptions, rate limits, environment problems).
4. Write AUDIT_DIR/10-benchmark/runs/<TASK_ID>-<CONDITION>-<RUN_NUMBER>.md with those
   fields, the starting commit identifiers (as recorded, no repository URLs), the
   Claude Code version and model if recorded, and the date.
5. Append one line to AUDIT_DIR/10-benchmark/runs-index.md: task, condition, run,
   verification result, cost, duration, file name. Create the index if missing.
6. Confirm no secrets or prompt text containing sensitive content were written.

Do not compute conclusions or compare conditions; that is done later in the
analysis step. End by listing the files written.
```
