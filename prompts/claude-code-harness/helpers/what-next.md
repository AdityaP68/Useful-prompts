# What Should I Do Next?

**When to use:** Whenever you come back after a break, lose track of where you are, or a session ended unexpectedly. It looks at the persisted artifacts and tells you the next manual step.
**Session:** EITHER
**Mutation:** NONE

## Copy/Paste Prompt

```text
AUDIT_DIR: <path outside every repository>

Work out where I am in the Claude Code harness workflow and tell me the next manual
step. Read-only: do not modify anything and do not start any phase.

Workflow (major prompts 01-10, helpers in brackets):
  Each analysis phase 01..07 follows the same gate:
    run the phase -> [Phase Completeness Check] -> [Freeze Phase]
    -> if the conversation is large, start a NEW session
    -> [Fresh Session Recovery Test]
         INSUFFICIENT -> [Repair Handoff] -> repeat the recovery test in a new session
         SUFFICIENT   -> run the next phase in that same fresh session
  Order: 01 inventory -> 02 history mining -> 03 hierarchy -> 04 skills ->
  05 subagents -> 06 hooks and MCPs -> 07 harness design -> HUMAN APPROVAL of P0
  -> baseline benchmark (10, baseline round) -> 08 implement approved P0
  (fresh session) -> Completeness Check -> [Independent Review Handoff] -> Freeze
  -> 09 adversarial review (separate fresh session) -> human decides which fixes
  to apply -> 10 benchmark again (harness round) -> compare.
  Interrupt helpers: [Stop Scope Creep], [Diagnostic Only], [Safe Historical
  Analysis] (during 02), [Construct Evaluator] (during 04-06), [Record Benchmark]
  (after each benchmark run).

Do this:
1. Read AUDIT_DIR/STATUS.md if it exists. List the files and sizes in AUDIT_DIR and
   AUDIT_DIR/handoff (names, sizes, dates only), then open only the handoff files.
2. Determine for each phase: NOT STARTED / IN PROGRESS / COMPLETED / VALIDATED
   (VALIDATED means a fresh-session recovery test returned SUFFICIENT), citing the
   evidence (which files exist, which are missing or look partial). If STATUS.md and
   the files disagree, say so and trust the files.
3. State the current position and the single next step, naming the exact helper or
   major prompt, whether it needs a NEW session, and what I must paste first.
4. List blockers: missing artifacts, an unanswered open question, a missing human
   approval, a failed recovery test, a stale handoff.
5. If something looks wrong (a report that is too thin, a phase marked done without a
   handoff), recommend the corrective helper before moving on.

Keep the answer under 25 lines.
```
