# 03 — Design the Context & Memory Hierarchy

**Mode:** read-only design. **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/01-inventory.md`, `<AUDIT_DIR>/02-history-findings.md`, plus targeted inspection of manifests and docs. **Writes:** `<AUDIT_DIR>/03-hierarchy.md` only.

---

Design where each kind of knowledge and instruction for `<PROJECT>` should live, using this hierarchy:

```
USER
  ↓
WORKSPACE
  ↓
REPOSITORY
  ↓
TASK

HISTORY = searchable, non-default context
```

If either report from earlier prompts is missing, say so and either run a minimal version of the missing analysis or ask me. Re-check the current Claude Code documentation for loading behaviour and precedence before relying on them.

## Rules

- Do NOT modify anything except the report.
- A workspace-level configuration may deliberately be personal. Repository configuration must remain usable by someone who clones only that repository.
- `CLAUDE.md` is orientation and instructions, **not an encyclopedia**.
- Do not record facts that Claude can cheaply discover by reading the code or manifests (directory listings, dependency lists, obvious naming, version numbers present in manifests).
- Verify before promoting: knowledge taken from old documents or past sessions must be checked against the current code/configuration before it is proposed as durable.
- Every proposed item needs evidence from `01` or `02`, or from inspection of the current workspace.

## Step 1 — Distinguish five kinds of content

| Kind | Nature | Typical home |
|------|--------|--------------|
| Instructions | What to do / not do; conventions; commands to use | `CLAUDE.md`, scoped rules |
| Knowledge | Facts about the system and decisions | Project documentation, linked from instructions |
| Procedural memory | How to perform a repeated workflow | Skills, scripts |
| Task / working memory | Current goal, decisions made, progress, next steps | Task state file (outside durable docs) |
| Historical / episodic | What happened before | History, archives; searchable, not loaded by default |

For each item you propose to keep, label which kind it is.

## Step 2 — Decide the content of each level

For each of the following, state what belongs, what must NOT be there, target size, when it is loaded, who owns it and whether it is shared or personal:

- **User `CLAUDE.md`**: personal working style and cross-project preferences only.
- **Workspace `CLAUDE.md`**: cross-repository orientation (how repositories relate, where to start for common change types, workspace-wide commands), nothing a single repository needs alone.
- **Repository `CLAUDE.md`**: the minimum a contributor or Claude needs to work in this repository alone (build/test/lint commands that are non-obvious, conventions that are not enforced by tooling, pitfalls with evidence, pointers to docs).
- **Scoped rules**: guidance that applies only to certain paths or file types (for example one language or one package), loaded only when relevant, if the installed version supports it.
- **Docs**: durable architectural knowledge and decisions, located where humans expect them, referenced (not copied) from instructions.
- **Task state**: where the active task's goal, constraints, decisions, files of interest, test status and next steps live, and how it survives compaction or a new session. Define its format and its deletion point.
- **History/archive**: what is retained, where, and how it is searched on demand.
- **Skills**: which of the repeated procedures are procedural memory rather than instruction (names only here; prompt 04 designs them).
- **Scripts**: deterministic operations that should be executed rather than reasoned about (names only here).

For a polyglot workspace, say which guidance is language-specific and where it should be scoped, and justify each language-specific item with evidence rather than assuming it.

## Step 3 — Precedence and conflict rules

Using the *current* documentation, state:

- the loading order and which scope wins when instructions conflict
- how imports/includes behave and any depth limits
- how files in parent directories, child directories and git worktrees are picked up
- what happens when a repository is opened on its own versus from `<WORKSPACE>`

Then define project conventions on top of that: a single source of truth per fact, no duplicated sentences across levels, lower levels may narrow but not contradict higher levels, and personal rules never override shared safety rules.

## Step 4 — Knowledge lifecycle

Define the lifecycle and who or what performs each transition:

```
discovery
 → task observation
 → verification
 → durable knowledge
 → correct scope
 → reusable procedure (only if it recurs)
 → archive / removal when obsolete
```

Specify: what counts as verification (reading current code, running a command, a test); who approves promotion to durable shared knowledge; how staleness is detected (for example, referencing file paths or commands that can be mechanically checked); and a lightweight review cadence. Keep this process small enough that it will actually be followed.

## Step 5 — Migration map

Using `01-inventory.md`, map existing constructs and document content to the proposed homes: which content moves, merges, gets trimmed, is archived or deleted. Show estimated always-loaded size before and after for sessions started in `<WORKSPACE>` and in each `<REPOSITORY>`.

## Output

Write `<AUDIT_DIR>/03-hierarchy.md`:

1. Principles and conventions
2. Content-kind classification
3. Level-by-level specification (tables as above) with evidence references
4. Proposed file tree using placeholders (no real names)
5. Precedence/conflict rules (with documentation references)
6. Knowledge lifecycle
7. Task-state design
8. Migration map and before/after context estimates (labelled as estimates)
9. Things deliberately NOT documented, and why
10. Open questions

Print a summary (at most 40 lines) in chat.
