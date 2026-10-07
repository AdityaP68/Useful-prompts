# HBENCH — Record Benchmark

**When to use:** After each individual H10 benchmark task run, or after a recovery test (HRECOVER) if you want to record what recovery cost, to capture its metrics in a consistent, comparable record.
**Session:** NEW is recommended (so recording overhead is not counted in the measured session): point it at the finished session. SAME also works if you tell it to report totals as of just before this prompt.
**Mutation:** ARTIFACTS ONLY (writes under `10-benchmark/` in the artifact root)
**Placeholders to fill:** optional. Leave `TASK_ID`, `CONDITION` and `RUN_NUMBER` blank and Claude will ask for what it cannot detect.

## Copy/Paste Prompt

```text
HARNESS WORKFLOW
Helper ID: HBENCH
Operation: Record Benchmark

This prompt was manually copied from an external prompt library. Do not assume that
library or its filenames are available in this environment. IDs such as H01 or HCHECK
are logical workflow identifiers, not file paths; do not try to open or look up a
prompt by ID or name.

TASK_ID: <short id from the benchmark task list, or leave blank>
CONDITION: <BASELINE | HARNESS | ABLATION: what was removed | RECOVERY, or leave blank>
RUN_NUMBER: <1, 2, 3 ... or leave blank>
MEASURED_SESSION: <session id, start time, or "this session"; leave blank to be asked>

Record one benchmark run. Do not run, repeat or fix the task. Do not modify any
repository or configuration. Write only under 10-benchmark/ in the harness artifact
root (AUDIT_DIR, a folder outside every repository; use the path stated earlier in this
session, otherwise find the folder that contains a workflow-state file such as STATE.md
or the 10-benchmark folder, otherwise ask me). Persistent artifacts here are the only
source of workflow state: do not look for an external prompt library, public file names
or old conversation transcripts as workflow state.

1. If CONDITION is RECOVERY, or the measured session began with the HRECOVER operation,
   record it as a recovery measurement: TASK_ID = RECOVERY-<phase ID that was recovered>.
   Otherwise use the task id and condition given above, asking me for any that are blank.
2. Locate the measured session's record using the history locations documented for this
   Claude Code version (and the product's usage reporting if the session is still
   open). Do not print transcript content.
3. Extract, where available, and write N/A (never an estimate presented as a
   measurement) where not: input tokens, output tokens, cache read tokens, cache
   creation tokens, cost, duration, Read calls, Grep/search calls, Glob calls, Bash
   calls, MCP calls, subagent calls, skill invocations, distinct files inspected,
   repeated reads, peak and total context volume, test executions, failed attempts. If
   this recording session is the measured session, report totals as of just before
   this prompt.
4. Ask me for what cannot be measured automatically and record my answers verbatim: did
   the result pass the task's pre-defined verification (PASS/FAIL), incorrect
   assumptions observed, regressions introduced, user corrections or interventions,
   and anything unusual (interruptions, rate limits, environment problems). For a
   recovery measurement, ask whether the recovery verdict was SUFFICIENT.
5. Write 10-benchmark/runs/<TASK_ID>-<CONDITION>-<RUN_NUMBER>.md with those fields, the
   starting commit identifiers (as recorded, no repository URLs), the Claude Code
   version and model if recorded, and the date.
6. Append one line to 10-benchmark/runs-index.md: task, condition, run, verification
   result, cost, duration, file name. Create the index if missing.
7. Confirm no secrets or sensitive prompt text were written.

Do not compute conclusions or compare conditions; that is done later in the analysis
step. End by listing the files written.
```
