# 01 — Inventory Existing Claude Code Constructs

**Mode:** read-only. **Run from:** `<WORKSPACE>` (or from `<REPOSITORY>` if you only use a single repository).
**Reads:** the live environment. **Writes:** `<AUDIT_DIR>/01-inventory.md` only.

---

You are auditing the Claude Code configuration that actually exists on this machine for `<PROJECT>`. Inspect the real environment before making any recommendation. Do not assume Claude Code behaves the way you remember; verify.

## Rules

- Do NOT modify, create, move or delete anything except the report file `<AUDIT_DIR>/01-inventory.md`. `<AUDIT_DIR>` must be outside every repository; if it is not set, ask me for it.
- Do NOT print secrets. If a file contains tokens, keys, headers or credentials, note only that it does and where.
- A personal, workspace-level configuration is valid. Do NOT assume everything should be repository-owned. Another developer may clone only a single repository and maintain their own higher-level workspace.
- Treat backup copies, scratch checkouts and generated directories as non-canonical unless I say otherwise. If you cannot tell which directories are canonical, report the ambiguity instead of guessing.
- Do not read whole source trees. Only read configuration, instruction files and manifests.

## Step 0 — Establish what this Claude Code version supports

1. Determine the installed Claude Code version and OS.
2. Consult the installed CLI help and the current official Claude Code documentation (and in-app commands such as those that report memory, context, hooks, MCP servers, agents, permissions and diagnostics, where they exist in this version).
3. Write down which construct types and file locations this version actually supports, and which settings keys and hook events it recognizes. Everything below is a checklist of *things to look for*; if this version supports additional constructs, audit those too. If something on the checklist does not exist in this version, say so.

## Step 1 — Discover

Look in every scope, using the locations the current documentation specifies (user home directory, `<WORKSPACE>`, each `<REPOSITORY>`, and enterprise/managed scopes if present). Cover all of:

- user-level configuration and instructions
- workspace-level configuration (a directory above the repositories may have its own configuration; check every ancestor directory up to the filesystem root and the user home)
- repository-level configuration
- the `CLAUDE.md` hierarchy, including imports/includes and local (uncommitted) variants
- scoped rules/instructions (path- or glob-scoped files, if supported)
- Skills (user, workspace, repository, plugin-provided) and Skill Creator artifacts (drafts, evals, benchmarks, workspaces)
- subagents (user, project, plugin-provided)
- hooks (every settings file that can define them, plus any scripts they call)
- MCP servers (every file that can define them; user, project, local and plugin-provided)
- plugins and marketplaces
- settings and local settings, including permissions (allow/deny/ask rules), environment variables, model and output settings
- task/worktree constructs (git worktrees created for Claude, task lists, plan files, scratch notes)
- session/history artifacts (where transcripts live, how many, how large, retention settings)
- ignore files or other mechanisms that hide content from Claude
- any other currently supported construct you found in Step 0

For each repository also record: git root vs nested location, whether configuration is tracked or git-ignored, and whether the repository is usable on its own without the workspace.

## Step 2 — Analyze each construct

For every construct record:

| Field | Meaning |
|-------|---------|
| Location | Path relative to `<WORKSPACE>` or `~` (never absolute machine paths in the report) |
| Scope | user / workspace / repository / task / plugin / managed |
| Active? | Is it actually loaded or enabled right now? How do you know? |
| Purpose | One sentence |
| Loading behaviour | Always at startup / on file access / on demand / on trigger / manual |
| Sharing | Personal vs shareable (tracked in git, shared with the team) |
| Duplication / conflicts | Same instruction in several places; contradictory instructions; shadowing/precedence effects |
| Stale / dead | References to files, commands, tools, versions or processes that no longer exist |
| Context / token cost | Approximate size and whether it is always-loaded; estimate with a rough chars÷4 tokens figure and label it an estimate |
| Verdict | KEEP / CHANGE / REMOVE / MOVE / INVESTIGATE |
| Evidence | The specific observation that justifies the verdict |

Verify claims against reality: if an instruction says "run command X", check that X exists; if it names a directory or tool, check that it is still there.

## Step 3 — Cross-cutting analysis

- **Effective context at startup:** for a session started in `<WORKSPACE>`, and separately in each `<REPOSITORY>`, list exactly what is always loaded and its approximate size. Use the product's own context reporting if available to cross-check your estimate.
- **Precedence and conflicts:** which instructions win when scopes disagree, per the current documentation.
- **Duplication map:** knowledge that appears in more than one place.
- **Misplaced items:** workspace-specific content inside a repository, repository-specific content in user scope, personal preferences in shared files, and secrets anywhere.
- **Portability:** which repository configuration breaks if someone clones only that repository.
- **Dead configuration:** hooks calling missing scripts, MCP servers that fail to start, permissions for removed tools, unused Skills/agents.

## Output

Write `<AUDIT_DIR>/01-inventory.md` with these sections, then print a short summary (under 40 lines) in chat:

1. Environment & version facts (what this Claude Code version supports)
2. Scope map (a tree of what exists at each level, using placeholders for names)
3. Construct inventory table (one row per construct, fields above)
4. Effective always-loaded context per launch location
5. Conflicts, duplication, stale and misplaced items
6. Portability findings
7. Verdict summary: counts by KEEP / CHANGE / REMOVE / MOVE / INVESTIGATE
8. Open questions for the human
9. Hand-off notes for prompt 02 (which history locations exist, how to find them, anything needing care)

Do not propose a new design yet. This prompt establishes facts.
