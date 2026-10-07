# Construct Evaluator

**When to use:** To decide whether a specific candidate (or an existing item) should exist as a Skill, subagent, hook, script, MCP server, or `CLAUDE.md`/rule entry. Useful inside prompts 04, 05 and 06 for borderline candidates, and during review.
**Session:** EITHER
**Mutation:** NONE (the verdict is given in chat; write it into a report only if you ask)

## Copy/Paste Prompt

```text
TYPE: <SKILL | SUBAGENT | HOOK | SCRIPT | MCP | CLAUDE.MD/RULE>
CANDIDATE: <one-paragraph description, or the path of an existing item>
AUDIT_DIR: <path outside every repository, if earlier reports exist there>

Evaluate this candidate. Read-only: do not create or change anything.

Step 0. Check the installed Claude Code version and current documentation for what
TYPE supports (format, locations, loading behaviour, limits). Do not rely on memory.

Step 1. Gather evidence: from the history report in AUDIT_DIR if it exists, and from
the current workspace. State how many times, over what period, and at what cost the
need has actually arisen. No evidence means NEEDS EVIDENCE, not BUILD.

Step 2. Common tests (answer each briefly):
- What exact problem does it solve, and what happens without it?
- Is there a cheaper construct? Compare against all six types (a script instead of a
  skill, a rule instead of a skill, a skill instead of a subagent, and so on).
- Scope: user / workspace / repository. Does it still work for someone who clones
  only one repository?
- Overlap or conflict with existing constructs.
- Always-loaded context cost, and per-use cost.
- Maintenance burden and what will make it stale.
- How would we know it works (verification) and how would we know it is unused?

Step 3. Type-specific tests (apply only the section for TYPE):
- SKILL: recurring multi-step procedure? Description precise enough to trigger
  correctly and not collide with other skills? Main file short, details in
  on-demand references, deterministic parts in scripts? Not a one-command wrapper,
  not a copy of docs or CLAUDE.md?
- SUBAGENT: does it repeatedly consume substantial context, can it be isolated,
  return a compact result, benefit from independent verification or parallelism?
  Input contract, minimal tools, read-only if possible, output size bound, and a
  briefing cost lower than the context it saves?
- HOOK: is it deterministic lifecycle behaviour that must happen automatically? Cost
  and frequency of the event, blocking or not, failure mode (fail-open or closed),
  timeout, portability across shells and operating systems, and why not a skill or
  script?
- SCRIPT: is the work deterministic? Inputs/outputs defined, cross-platform,
  no secrets, clear failure output, small enough to review, invoked by whom
  (a skill, a hook, or manually)?
- MCP: capability or retrieval Claude cannot reach cheaply otherwise? Number of tools
  and definition overhead, response size, credentials and data leaving the machine,
  whether it can be loaded only when needed, and would a script or skill do?
- CLAUDE.MD/RULE: needed on most tasks in that scope? One or two lines? Not
  discoverable from code? Not already enforced by tooling? Correct level (user,
  workspace, repository, path-scoped rule)? Does it conflict with another level?

Step 4. Verdict, exactly one of:
- BUILD AS <TYPE> (priority P0/P1/P2) with a minimal spec: name, scope, trigger or
  event, contents outline, size target, dependencies, verification, rollback
- BUILD AS <OTHER TYPE> instead (explain why)
- DO NOT BUILD (explain what already covers it)
- NEEDS EVIDENCE (state exactly what to measure and for how long)

Be skeptical: prefer not building, and prefer the cheapest construct that works.
```
