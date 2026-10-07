# 05 — Discover Candidate Subagents

**Mode:** read-only design. **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/01-inventory.md`, `02-history-findings.md`, `03-hierarchy.md`, `04-skill-candidates.md`. **Writes:** `<AUDIT_DIR>/05-subagent-candidates.md` only.

---

HARNESS WORKFLOW  
Prompt ID: H05  
Phase: Subagent Discovery  
Previous Major Phase: H04 — Skill Discovery  
Next Major Phase: H06 — Hooks and MCP Audit  
Primary artifact: <AUDIT_DIR>/05-subagent-candidates.md  

This prompt was manually copied from an external prompt library.  
Do not assume that library or its filenames are available in this environment.  
The Prompt ID is a logical workflow identifier, not a local file path.  
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the  
harness artifact root (a folder outside every repository). Earlier phases, if any,  
left their results there; inspect those artifacts, not any external files.  
When this phase is finished, end your final message with:  
"H05 complete. Recommended next operation: HCHECK - Phase Completeness Check."  

---

Decide which subagents, if any, `<PROJECT>` needs. The default answer is **none**. Do NOT invent generic agents (for example "code reviewer", "debugger", "architect") because they sound useful.

## Rules

- Do NOT create or modify agent definitions in this phase.
- Check the current documentation for: agent file format and locations, which fields are supported (tools, model, permissions, etc.), whether subagents can spawn others, how results return to the parent, and how agent descriptions are loaded. Do not assume.
- Evidence must come from history (`02-history-findings.md`) or inspection of current work patterns. Quote counts and cost, not impressions.
- Subagents have costs: they start without the parent's context, duplicate exploration, and add tokens for the briefing and the result. They only pay off in the cases below.

## A responsibility justifies a subagent only if at least one holds

1. It **repeatedly consumes substantial context** (large searches, many file reads, long logs) that the parent does not need afterwards.
2. It **can be isolated**: well-defined input, no need for the parent's conversation.
3. It **can return a compact result** (a short summary, a list of paths, a verdict).
4. It **benefits from independent verification** (a fresh context checks work without the author's bias).
5. It **materially benefits from parallel execution** (independent items processed concurrently).

For each candidate, state which criteria hold and show the evidence.

## Step 1 — Evaluate existing agents and observed subagent use

From the inventory and history: which agents exist, how often each is invoked, what each costs relative to its parent session, how large the returned results are, and whether results were actually used. Verdict: KEEP / CHANGE / REMOVE.

## Step 2 — Candidates

For each candidate:

| Field | Content |
|-------|---------|
| Responsibility | One sentence |
| Evidence | Sessions, frequency, context consumed in the parent today |
| Required input | Exactly what the parent must pass (it will not see the parent's context) |
| Allowed tools | Minimal set; justify each |
| Output contract | Exact structure of the returned result |
| Expected output size | Upper bound (lines/tokens) |
| Read-only vs write | Strongly prefer read-only |
| Invocation conditions | When the parent should delegate |
| Non-invocation conditions | When doing the work in the main session is cheaper |
| Expected context savings | Estimated tokens and basis; also estimated added cost of briefing and result |
| Skill overlap | Whether a Skill, or a Skill that calls the agent, is the better home |

## Step 3 — Agent vs Skill vs Script

For every candidate, compare the three options explicitly and choose one:

- **Script**: if the work is deterministic, it should not use a model at all.
- **Skill**: if it is a procedure executed in the main context with progressive loading.
- **Subagent**: only if isolation, compact return, independent verification or parallelism is required.

State the decision rule you used and show the table. If a Skill can invoke a subagent for one heavy step, prefer that over a standalone agent that overlaps the Skill's trigger.

## Step 4 — Minimal set

Reduce to the smallest justified set. Merge candidates with the same input/output shape. Reject candidates that rely on the parent's context or whose results are too large to return compactly. Rank P0/P1/P2 and list rejected candidates with reasons. An empty P0 list is a valid and common outcome.

## Step 5 — Safety and governance

For each kept candidate: permission implications, whether it can modify files or run commands, how its output is validated by the parent, and how to detect that it has become stale or unused.

## Output

Write `<AUDIT_DIR>/05-subagent-candidates.md`:

1. Subagent platform facts for this version
2. Evaluation of existing agents / observed subagent usage
3. Candidate details (fields above)
4. Agent vs Skill vs Script decision table
5. Minimal recommended set with P0/P1/P2
6. Rejected candidates
7. Open questions

Print a summary (at most 30 lines) in chat.
