# 04 — Discover Candidate Skills

**Mode:** read-only design. **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/01-inventory.md`, `02-history-findings.md`, `03-hierarchy.md`. **Writes:** `<AUDIT_DIR>/04-skill-candidates.md` (and scratch drafts under `<AUDIT_DIR>` only).

---

HARNESS WORKFLOW  
Prompt ID: H04  
Phase: Skill Discovery  
Previous Major Phase: H03 — Context Hierarchy Design  
Next Major Phase: H05 — Subagent Discovery  
Primary artifact: <AUDIT_DIR>/04-skill-candidates.md  

This prompt was manually copied from an external prompt library.  
Do not assume that library or its filenames are available in this environment.  
The Prompt ID is a logical workflow identifier, not a local file path.  
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the  
harness artifact root (a folder outside every repository). Earlier phases, if any,  
left their results there; inspect those artifacts, not any external files.  
When this phase is finished, end your final message with:  
"H04 complete. Recommended next operation: HCHECK - Phase Completeness Check."  

---

Identify the Skills that `<PROJECT>` genuinely needs, using the installed Skill Creator capabilities where available. Skills must be derived **primarily from repeated historical workflows**, not invented.

## Rules

- Do NOT create Skills in any real Skill directory in this phase. Drafts and evals, if you make any, go under `<AUDIT_DIR>`. Implementation is prompt 08.
- First check what Skill Creator tooling is installed and what it can do in this version (drafting, evals, benchmarking, description optimization). Use only what exists. If it is absent, say so and fall back to the current documentation's Skill format.
- Check the current documentation for the Skill file format, frontmatter fields, locations by scope, how descriptions are loaded, and how Skill bodies and bundled files load. Do not assume.
- Every candidate needs evidence: occurrences, spread over time, and cost per occurrence, taken from `02-history-findings.md`. No evidence, no candidate.
- Existing Skills from `01-inventory.md` must be evaluated first: keep, change, merge or remove them before proposing new ones.

## Prefer

- A few high-value Skills over many narrow ones.
- Progressive disclosure: a short `SKILL.md` that states when to use it and the ordered workflow, with detail in reference files loaded only when a step needs them, and deterministic work in scripts.
- Skills whose value is *procedure* (ordered steps, checks, decision points), not facts.

## Avoid

- Language-specific Skills without evidence that the workflow differs by language in a way that matters
- One-command Skills (a script or a line in `CLAUDE.md` is cheaper)
- Skills that duplicate `CLAUDE.md` content, hooks, or each other
- Skills with overlapping trigger descriptions
- One-off workflow Skills
- Skills whose body mostly restates documentation (reference the docs instead)

## Step 1 — Evaluate existing Skills

For each: usage frequency from history, trigger accuracy (fired when it should / failed to fire / fired wrongly), size, duplication, staleness, verdict KEEP / CHANGE / MERGE / REMOVE, with evidence.

## Step 2 — Propose candidates

For each candidate, specify all of:

| Field | Content |
|-------|---------|
| Name | Short, verb-oriented, unambiguous |
| Scope | user / workspace / repository (justify; consider portability and who else uses it) |
| Historical evidence | Number of sessions, period, representative cost per run |
| Trigger | The situations and phrasings that should invoke it |
| Problem | What goes wrong or costs too much without it |
| Expected savings | Tokens/tool calls/time and correctness, labelled as an estimate with reasoning |
| Ordered workflow | Numbered steps, marking which are deterministic and which need judgement |
| `SKILL.md` responsibility | What the main file contains and its target size |
| Progressive references | Files loaded only at specific steps, and which step loads each |
| Deterministic scripts | What is delegated to scripts (inputs/outputs, cross-platform) |
| MCP dependencies | Any server/tool it relies on, and fallback if unavailable |
| Reasoning left to Claude | The judgement calls |
| Verification | How the Skill's result is checked (tests run, outputs compared, invariants confirmed) |
| Non-trigger conditions | When it must NOT be used, and nearby Skills it must not collide with |
| Do-not-copy list | Content that must stay in docs/CLAUDE.md rather than be copied into the Skill |

## Step 3 — Overlap and cost check

Build a matrix of candidate-vs-candidate and candidate-vs-existing-construct trigger overlap. Estimate the always-loaded cost of all descriptions together. Resolve overlaps by merging, narrowing or dropping.

## Step 4 — Rank

- **P0**: strong evidence, high recurrence, clear savings, low maintenance
- **P1**: good evidence, moderate savings or higher maintenance
- **P2**: plausible but weak evidence; revisit after benchmarking

Also list candidates that were considered and **rejected** and why (including those better served by a script, hook, rule or documentation).

## Step 5 — Test plan

For each P0 candidate, propose 3–5 realistic prompts that should trigger it, 2–3 near-miss prompts that should not, and the verification you would run with Skill Creator (if available) to confirm trigger accuracy and output quality.

## Output

Write `<AUDIT_DIR>/04-skill-candidates.md`:

1. Skill platform facts for this version
2. Existing Skills evaluation
3. Candidates (one subsection each, fields above)
4. Overlap matrix and aggregate description cost
5. Ranking (P0/P1/P2) and rejected candidates
6. Test plan for P0
7. Open questions

Print a summary (at most 40 lines) in chat.
