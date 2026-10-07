# 02 — Mine Historical Claude Code Usage

**Mode:** read-only analysis. **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/01-inventory.md` (for history locations) and local session history. **Writes:** files under `<AUDIT_DIR>` only (a throwaway script, an aggregate index, and `02-history-findings.md`).

---

HARNESS WORKFLOW  
Prompt ID: H02  
Phase: Historical Usage Mining  
Previous Major Phase: H01 — Inventory Existing Constructs  
Next Major Phase: H03 — Context Hierarchy Design  
Primary artifact: <AUDIT_DIR>/02-history-findings.md  

This prompt was manually copied from an external prompt library.  
Do not assume that library or its filenames are available in this environment.  
The Prompt ID is a logical workflow identifier, not a local file path.  
In the text below, "prompt NN" means harness phase HNN, and AUDIT_DIR is the  
harness artifact root (a folder outside every repository). Earlier phases, if any,  
left their results there; inspect those artifacts, not any external files.  
When this phase is finished, end your final message with:  
"H02 complete. Recommended next operation: HCHECK - Phase Completeness Check."  

---

You are analyzing how Claude Code has actually been used on `<PROJECT>` to find repeated work, waste and candidate abstractions. Evidence comes first; opinions come last.

## Rules

- Do NOT feed whole transcript directories to the model. History can be very large and can contain secrets and source code. Preprocess it **programmatically** and look at aggregates and a few representative excerpts.
- Do NOT print raw transcript content unless it is short, necessary as evidence, and free of secrets. Never reproduce credentials, tokens or personal data. Redact when quoting.
- Do NOT modify any repository, any Claude Code configuration, or any history file. Only read history; write only under `<AUDIT_DIR>`.
- Do NOT treat Claude's past statements as true. They are evidence of *what was done*, not of how the code works.
- Do NOT create abstractions from one-off events. Every finding needs a frequency and a time spread.
- Use a cross-platform approach (for example a small Python script using only the standard library). Do not rely on shell tools that are missing on other operating systems.
- On Windows, normalize path separators and drive letters, compare paths case-insensitively, open files as UTF-8, and confirm which environment (native or WSL) owns the history being analyzed.

## Step 0 — Locate and characterize history

Using the history locations noted in `01-inventory.md` (and the current documentation if they are missing), determine:

- where session transcripts are stored and how they are organized (per project/directory?)
- the file format and which record types exist (user messages, assistant messages, tool calls, tool results, usage/cost fields, model identifiers, timestamps, working directory, branch, session/sub-session links)
- how many sessions exist for this workspace and for each `<REPOSITORY>`, the date range, and the size distribution
- whether sessions can be attributed to a repository (by recorded working directory, by paths touched)
- retention/cleanup settings that may have deleted older data
- what is NOT recorded (so findings are not over-claimed)

Inspect the schema by sampling a few records' keys, not by reading whole files. Report your sampling method.

## Step 1 — Build an index (programmatic)

Write a script in `<AUDIT_DIR>` that streams the transcripts and emits compact aggregates (JSON/CSV), without loading everything into memory. Capture per session where available:

- session id, start/end time, duration, working directory, repository attribution, branch
- model(s) used; input / output / cache-read / cache-creation tokens; cost if recorded
- counts per tool (Read, Grep, Glob, Bash, Edit/Write, MCP tools, subagent/Task calls, Skill invocations, others)
- normalized tool-call targets: file paths (relative to `<WORKSPACE>`), glob patterns, search patterns, and a *normalized* form of shell commands (command name + subcommand, arguments stripped of paths and secrets)
- repeated reads of the same file within a session and across sessions
- tool-result sizes (large outputs) and which tools produce them
- failures: tool errors, retries, test failures, user interruptions, user corrections ("no, instead…", rejected edits)
- user prompt categories (see Step 2) assigned by simple, inspectable heuristics, which you document

Run the script, check it on one session by hand, then run it over everything. Keep aggregates, discard nothing in the originals.

## Step 2 — Compute statistics

Report, with sample sizes and the caveat that history is a biased sample:

- sessions per repository and per month; median and tail session size in tokens and tool calls
- token and cost distribution: where the top 10% of sessions spend
- most-read files and directories (top N), and files read in ≥ K distinct sessions
- most frequent search patterns and the directories they target
- most frequent normalized Bash commands (build, test, lint, git, package manager, other)
- ratio of exploration calls (Read/Grep/Glob) to editing calls, per session
- cross-repository navigation: sessions touching more than one `<REPOSITORY>`, and what typical sequences look like
- largest tool outputs and their sources
- subagent usage: counts, which tasks, their cost relative to the parent session
- Skill usage and MCP tool usage: counts and results sizes (including zero-use items)
- correction/rework rate: sessions with rejected edits, repeated failed attempts, or explicit user corrections

## Step 3 — Cluster repeated workflows

Cluster sessions (or segments of sessions) by similar tool-call sequences, targets and user intent. For each cluster with enough support, inspect **2–3 representative sessions** (read selectively: the opening prompt, the tool-call outline, the outcome) and describe:

- the workflow in plain language and its ordered steps
- how many sessions and over what period it recurs
- how much it costs (tokens/tool calls) per occurrence
- what the model had to rediscover each time (architecture, file locations, commands, conventions)
- what went wrong, was corrected or abandoned (failed approaches)
- which steps were deterministic and could be computed rather than reasoned about

Look specifically for the following, and report "not observed" when absent:

- repeated Read/Grep/Glob/Bash sequences
- repository and architecture rediscovery
- repeated reads of the same files
- cross-repository navigation patterns
- debugging workflows
- implementation workflows
- testing and verification workflows
- Git operations
- documentation workflows
- dependency/library/version changes
- API/event/contract investigation
- failed approaches and dead ends
- large tool outputs and irrelevant context
- expensive agent usage
- repeated deterministic operations (the same command or computation performed many times)

## Step 4 — Classify each finding

Assign exactly one primary classification (and note secondary ones):

- **CLAUDE.md / scoped rule**: a short, durable instruction or orientation fact needed on most tasks in a scope
- **Skill**: a repeated multi-step procedure that benefits from progressive loading
- **Subagent**: an isolated, context-heavy responsibility that returns a compact result
- **Hook**: a deterministic lifecycle action that should happen automatically
- **Script**: deterministic computation that should not involve model reasoning
- **MCP capability**: indexed or external retrieval/capability not reachable cheaply otherwise
- **Knowledge / documentation**: durable truth that belongs in project docs
- **Task-state memory**: temporary working state that must survive compaction or resumption
- **No abstraction**: one-off, rare, already cheap, or not worth the maintenance

Require evidence thresholds: state the minimum recurrence you used (for example, at least N sessions across at least M distinct weeks) and justify it for the size of the history. Findings below the threshold go in an "Observed but not justified" list.

## Step 5 — Validate against the current environment

For every finding that implies a fact about the code or configuration (a command, a directory, a convention), check that the fact is still true today by inspecting the current repository. Mark each as VERIFIED, STALE or UNVERIFIED.

## Output

Write `<AUDIT_DIR>/02-history-findings.md` with:

1. Data availability and method (including limitations and bias)
2. Aggregate statistics (tables)
3. Workflow clusters (one subsection each: description, evidence/frequency, cost, rediscovery, failures, deterministic steps)
4. Classified findings table: finding → classification → evidence/frequency → expected benefit → confidence
5. Observed but not justified (below threshold)
6. Waste inventory: top sources of irrelevant context, repeated reads, large outputs, expensive agents
7. Baseline metrics worth tracking in prompt 10 (what is measurable from history in this environment)
8. Open questions

Print a summary of at most 40 lines in chat. Keep the index script in `<AUDIT_DIR>` for re-use by prompt 10. Redact any identifiers you would not want published.
