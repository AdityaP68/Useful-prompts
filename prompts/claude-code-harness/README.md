# Claude Code Harness Audit & Design

A reusable prompt workflow for empirically designing a Claude Code engineering harness from your **actual** project structure and **actual** historical usage.

It is company-agnostic and works for single repositories, monorepos and multi-repo workspaces, in polyglot codebases (Java, JavaScript, TypeScript, Python, YAML, and others).

## What this is

Ten prompts, run in order, that take you from "I have an organically grown Claude Code setup" to "I have a small, evidence-driven, benchmarked harness":

| # | Prompt | Mode | Produces |
|---|--------|------|----------|
| 01 | `01-inventory-existing-constructs.md` | read-only | Inventory of every Claude Code construct, with a KEEP/CHANGE/REMOVE/MOVE/INVESTIGATE verdict |
| 02 | `02-mine-historical-usage.md` | read-only | Evidence-backed catalogue of repeated work, waste and candidate abstractions |
| 03 | `03-design-context-hierarchy.md` | read-only | USER → WORKSPACE → REPOSITORY → TASK placement design and knowledge lifecycle |
| 04 | `04-discover-skills.md` | read-only | Ranked Skill candidates (P0/P1/P2) |
| 05 | `05-discover-subagents.md` | read-only | A minimal, justified subagent set (possibly empty) |
| 06 | `06-audit-hooks-and-mcps.md` | read-only | Hook and MCP keep/prune/add decisions |
| 07 | `07-synthesize-native-harness.md` | read-only | The final native harness design, P0/P1/P2 plan, and the problems native constructs cannot solve |
| 08 | `08-implement-p0.md` | **writes** | Implementation of the *approved* P0 only |
| 09 | `09-adversarial-review.md` | read-only | Ranked defects; bias toward deletion |
| 10 | `10-benchmark-harness.md` | measures | Before/after comparison on real tasks |

## Philosophy

Do not start by inventing dozens of Skills, agents and MCP servers. Observe real work first, then pick the cheapest construct that fits:

| Need | Construct |
|------|-----------|
| Persistent instruction or orientation | `CLAUDE.md` / scoped rules |
| Repeated multi-step procedure | Skill |
| Isolated, context-heavy responsibility returning a compact result | Subagent |
| Automatic, deterministic lifecycle action | Hook |
| Deterministic computation | Script |
| External or indexed capability / retrieval | MCP server |
| Durable project truth | Documentation |
| Temporary, ongoing state | Task memory |
| Obsolete evidence | History / archive |

Each construct costs something: always-loaded text, tool definitions, maintenance, or the risk of triggering at the wrong time. A construct earns its place only with evidence that it removes more cost than it adds.

### Primary optimization target

**Cost per successfully verified engineering task**, not raw token count. A cheap wrong answer is more expensive than a costly correct one.

### Progressive disclosure

Start with the smallest useful context and load deeper material only when the task requires it:

1. Always loaded: short orientation and hard rules.
2. Loaded on demand: Skill bodies, scoped rules, reference files, documentation.
3. Never loaded by default: history, archives, large generated output.

## The multi-level model

```
USER
  ↓
WORKSPACE
  ↓
REPOSITORY
  ↓
TASK

(HISTORY: searchable, never loaded by default)
```

- **User**: your personal preferences and habits, valid in every project.
- **Workspace**: the directory that contains one or more repositories. Cross-repository relationships and personal workflows live here.
- **Repository**: knowledge any contributor needs to work in that repository alone.
- **Task**: temporary working state for the current piece of work.

Workspace configuration can intentionally remain **personal**. Another developer may clone only a single repository and maintain their own higher-level workspace, so repository-level configuration must stay independently useful and must never depend on a particular workspace layout.

## What this phase deliberately does NOT do

This phase does not build custom Tree-sitter indexing, LSP integration, code graphs, vector databases or context routers. Those are considered **only after** the native harness has been benchmarked (prompt 10) and the remaining bottlenecks are identified (prompt 07, "problems native constructs cannot solve").

## Why not just install every Skill and MCP?

- Tool and Skill descriptions are loaded into context so that Claude can decide when to use them. Every addition has an always-on cost, whether or not it is used.
- More tools and overlapping Skills make selection less reliable, not more.
- MCP tool responses can be very large, and each call can pull irrelevant material into the context.
- Every construct needs maintenance and can go stale; stale guidance is worse than none.
- Constructs that were never justified by observed work are rarely used but always paid for.

Prefer a small surface, justified by repeated evidence.

## Recommended execution order

```
01 → 02 → 03 → 04 → 05 → 06 → 07
                                │
                          human review
                                │
                                ▼
                               08 → 09 → (apply fixes) → benchmark using 10
```

Run 10 once on the baseline (before 08) and again after 09's fixes are applied.

## Operating it manually

If you run these prompts by copying them from GitHub into Claude Code by hand, note that **this library and your Claude Code environment are separate**: the private Claude cannot open anything here. Every prompt therefore carries a stable **logical ID** (`H01`…`H10` for the major phases, `HCHECK`, `HFREEZE`, … for helpers), and the phases communicate through private artifacts the prompts create in your environment. Claude says "run HCHECK"; you look it up in the catalog and copy that file.

- **[PROMPT-CATALOG.md](PROMPT-CATALOG.md): start here.** Every ID with its GitHub file, order, session, and the workflow map
- [RUNBOOK.md](RUNBOOK.md): step-by-step manual operation, session boundaries, the check → freeze → recovery-test gate between phases
- [helpers/](helpers/README.md): short copy/paste prompts for the gaps between the major prompts
- [PROTOCOL.md](PROTOCOL.md): how IDs, self-identification headers, phase auto-detection and the workflow-state contract work
- [LIFECYCLE.md](LIFECYCLE.md): prompt maturity (DRAFT / TESTED / PROVEN), how to change proven prompts, and local status tracking
- [PROPOSED-CHANGES.md](PROPOSED-CHANGES.md): suggested edits to major prompts, not applied

## Shared conventions

- `<AUDIT_DIR>`: a directory **outside every repository** where reports are saved (for example, a scratch directory in your home folder). Each prompt reads the previous reports from there. Reports are Markdown files named after their prompt (`01-inventory.md`, `02-history-findings.md`, ...).
- Placeholders such as `<WORKSPACE>`, `<REPOSITORY>`, `<PROJECT>`, `<FEATURE>`, `<ACTIVE_TASK>` and `<COMPANY>` stand for your own names. Substitute them, or tell Claude the real values at the start of the session.
- Prompts 01–07, 09 and the measurement part of 10 are read-only with respect to your repositories. The only files they may create are reports under `<AUDIT_DIR>`.
- Every prompt tells Claude to **inspect the installed Claude Code version and its documentation** before relying on any construct, because file locations, hook events, settings keys and available slash commands change between versions.
- Shell commands in prompts are described in terms of intent. Use whatever works on your OS (macOS, Linux, Windows). A small cross-platform script (for example Python) is preferred over shell pipelines for history analysis.

## Windows notes

The pack is written to run on Windows, macOS and Linux. If you run it on Windows:

- **Native vs WSL.** Decide whether Claude Code runs natively (PowerShell/cmd/Git Bash) or inside WSL. They have separate home directories, separate `~/.claude` locations and separate session history. Audit the environment you actually use, and tell Claude which one it is at the start. If you use both, run the pack once per environment.
- **Locations.** Where the prompts say `~/.claude`, on native Windows that is under your user profile (`%USERPROFILE%\.claude` in cmd, `$env:USERPROFILE\.claude` in PowerShell). Prompt 01 makes Claude verify the real locations from the installed version's documentation rather than assume.
- **Scripts.** Prompt 02 asks Claude to write a small analysis script. Ask for Python using only the standard library, and run it with whichever launcher you have (`python` or `py`). Avoid `grep`, `awk` and `find` pipelines unless you know they exist on your shell.
- **Paths.** Reports use `<WORKSPACE>`-relative paths with forward slashes. Scripts should normalize backslashes and drive-letter prefixes and be case-insensitive when comparing paths.
- **Hooks and MCP launchers.** Prompt 06 checks that hook commands and server launchers work on your shell (for example `npx`-style launchers sometimes need a `cmd /c` wrapper on native Windows, and shell scripts need a shell that can run them). Verify against the current documentation.
- **Line endings.** Check Git's `core.autocrlf` and any `.gitattributes` before generating files with scripts, so that new configuration and scripts do not produce noisy diffs or break shell scripts with CRLF endings.
- **Long paths and locks.** Deep `node_modules` or build trees can hit path-length limits. Exclude them from analysis. Antivirus or editors holding files open can break moves and deletes; prompt 08 prefers copying to a backup before modifying.
- **Worktrees for benchmarks.** Prompt 10 uses a scratch branch or worktree. Use a short path (for example directly under a drive root or a short folder in your profile) to avoid long-path problems.
- **Permissions.** Run Claude Code as a normal user, not elevated. None of the prompts need administrator rights.

## Privacy and sharing

Session history can contain source code, secrets and other sensitive content. The prompts instruct Claude to aggregate locally, never print raw transcript content unnecessarily, and redact before anything is shared. Reports saved in `<AUDIT_DIR>` may still contain identifiers from your environment. Sanitize them before publishing or pasting them anywhere.

## Later, optional phase

After benchmarking, if the data shows retrieval or navigation costs that native constructs cannot reduce, consider a code-intelligence layer (syntax-tree indexing, language-server integration, a symbol/dependency graph, or a retrieval service exposed through an MCP server). That is a separate project with its own design and evaluation, and it is out of scope here.
