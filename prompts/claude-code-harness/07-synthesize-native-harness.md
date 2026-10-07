# 07 — Synthesize the Native Harness Design

**Mode:** read-only design. **Run from:** `<WORKSPACE>`.
**Reads:** all of `<AUDIT_DIR>/01` through `06`. **Writes:** `<AUDIT_DIR>/07-harness-design.md` only.

---

HARNESS WORKFLOW  
Prompt ID: H07  
Phase: Native Harness Synthesis  
Previous Major Phase: H06 — Hooks and MCP Audit  
Next Major Phase: Human approval of the P0 items, then H10 — Harness Benchmark (baseline round), then H08 — P0 Implementation  
Primary artifact: <AUDIT_DIR>/07-harness-design.md  

This prompt was manually copied from an external prompt library.  
Do not assume that library or its filenames are available in this environment.  
The Prompt ID is a logical workflow identifier, not a local file path.  
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the  
harness artifact root (a folder outside every repository). Earlier phases, if any,  
left their results there; inspect those artifacts, not any external files.  
When this phase is finished, end your final message with:  
"H07 complete. Recommended next operation: HCHECK - Phase Completeness Check."  

---

Combine the findings of prompts 01–06 into one coherent, minimal **Claude-native** harness design for `<PROJECT>`. Then stop: a human reviews this design before anything is implemented.

## Primary optimization target

Reduce **cost and tokens per successfully verified engineering task**. Do not optimize raw token count at the expense of correctness. Any proposed saving must say what verification protects correctness.

## Rules

- Do NOT modify anything except the report.
- Reconcile conflicts between earlier reports explicitly (for example, a Skill candidate that overlaps a hook). Prefer deleting or simplifying over adding.
- Re-check the current Claude Code documentation for any capability you rely on. Do not invent features. If a capability's availability is uncertain, label it UNVERIFIED and give a fallback.
- Use placeholders only (`<WORKSPACE>`, `<REPOSITORY>`, ...) in the design body; the human will map them to real names.
- Prior reports are inputs, not truth: spot-check at least the highest-impact claims against the current environment.

## Required coverage

Cover each of the following. For **every construct** use the same eight fields:

**WHY** · **SCOPE** · **WHEN LOADED** · **CONTENTS** · **WHAT MUST NOT BE INCLUDED** · **COST / CONTEXT IMPLICATION** · **OWNER** · **VERIFICATION**

Constructs and topics:

1. Hierarchy (USER → WORKSPACE → REPOSITORY → TASK; history as non-default)
2. `CLAUDE.md` at each level (with target sizes)
3. Scoped rules
4. Skills, and progressive disclosure within them (descriptions → body → references → scripts)
5. Scripts
6. Subagents (possibly none)
7. Hooks
8. MCP servers
9. Permissions (what is pre-approved, what is denied, which are personal vs shared)
10. Task state: format, location, creation, update points, deletion
11. Context compaction and session resumption: what must survive, how it is restored, how to avoid re-exploring after a reset
12. Knowledge lifecycle: discovery → task observation → verification → durable knowledge → correct scope → reusable procedure → archive/removal
13. Verification strategy: how the harness helps ensure that each task is verified (build, tests, type checks, linters, contract checks) across the repository languages that matter
14. Metrics: what to measure (cost per verified task, repeated discovery, tool calls, rework), how, and what thresholds would trigger a change
15. Sharing model: what is personal vs team-shared vs repository-owned; how another developer who clones only one repository benefits, and what happens if they have no workspace configuration

## Priorities

Assign every item **P0 / P1 / P2**:

- **P0**: high evidence, low risk, immediate savings or correctness gains; small enough to implement and review in one pass
- **P1**: valuable after P0 is validated
- **P2**: speculative or low-frequency; revisit after benchmarking

For each P0 item list the exact files to be created/changed (by placeholder path), the content outline, the expected always-loaded context before/after, the risk, and the rollback.

## What native constructs cannot solve

Explicitly identify problems the evidence shows **cannot be solved effectively** with Claude-native constructs. Examples to test against the evidence (do not assume they apply): symbol-level navigation across languages, finding all consumers of an API/contract/event across repositories, dependency/impact analysis, retrieval over very large codebases, build-graph awareness, semantic search over documentation and history.

For each: the evidence, the cost it causes today, what a solution would have to provide (inputs, outputs, latency, accuracy, freshness), and how success would be measured by prompt 10. These become **requirements for the later, optional custom-intelligence phase**. Do not design or build that phase here.

## Output

Write `<AUDIT_DIR>/07-harness-design.md`:

1. Executive summary (one page): what changes, why, expected effect, main risks
2. Design principles
3. Hierarchy and per-construct specification (eight fields each)
4. Final tree of proposed files by level (placeholders)
5. P0 / P1 / P2 plan with file-level detail for P0
6. Before/after always-loaded context estimates (labelled as estimates, with method)
7. Verification and metrics plan (feeds prompt 10)
8. Requirements for a later custom-intelligence phase
9. Risks and mitigations
10. Decisions the human must make (a short checklist to approve/reject/modify each P0 item)

Finish by printing the executive summary and the approval checklist, and **ask me which P0 items are approved**. Do not proceed to implementation.
