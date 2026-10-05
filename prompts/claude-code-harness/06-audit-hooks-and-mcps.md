# 06 — Audit Hooks and MCP Servers

**Mode:** read-only (inspection and measurement; do not change configuration). **Run from:** `<WORKSPACE>`.
**Reads:** `<AUDIT_DIR>/01-inventory.md`, `02-history-findings.md`, `03-hierarchy.md`. **Writes:** `<AUDIT_DIR>/06-hooks-and-mcps.md` only.

---

Audit and design the hooks and MCP servers for `<PROJECT>`. Keep both surfaces small.

## Rules

- Do NOT edit settings, hook scripts or MCP configuration. Do NOT start, stop or reconfigure servers beyond read-only inspection that the current documentation supports (for example listing servers and tools).
- Do NOT print credentials, tokens, headers or environment values that appear in configuration. Report only that they exist.
- Check the current documentation and installed version for: supported hook events, matcher syntax, hook input/output contract, blocking vs non-blocking semantics, timeouts, where hooks can be defined; MCP configuration scopes, how tool definitions are loaded into context, and whether tools can be loaded lazily or on demand in this version. Do not assume.
- Every recommendation needs historical or inspected evidence.

## Principle

- **Hook** = deterministic lifecycle behaviour that must happen automatically (policy enforcement, formatting at a cheap point, context injection at session start, notifications).
- **Skill** = a procedure Claude chooses to follow.
- **Script** = deterministic computation invoked on demand.
- **MCP** = a capability or retrieval source that Claude cannot reach cheaply otherwise.
- **`CLAUDE.md`** = persistent instruction or orientation.

If a script or Skill is cheaper or safer than a hook or MCP server, recommend that.

## Part A — Hooks

### Inventory

For each hook found (every settings scope, plugin-provided hooks included): event, matcher, command/script, scope, shareable or personal, what the script does (read it), and whether the script exists and is portable across macOS/Linux/Windows.

### Evaluate

| Field | Content |
|-------|---------|
| Trigger | Event + matcher; how often it fires per session (use history to estimate) |
| Action | What it does |
| Cost | Wall-clock time per firing, tokens added to context (if output is injected), compute cost |
| Failure behaviour | What happens if it errors, times out or the tool is missing; fail-open vs fail-closed |
| Blocking? | Can it block or alter the agent's action? Is that intended? |
| Why a hook | Why not a Skill, script or `CLAUDE.md` line |
| Evidence | Historical need (violations it prevents, repeated manual step it replaces) |
| Verdict | KEEP / CHANGE / REMOVE / ADD |

Flag expensive work on high-frequency events (for example running a full test suite, linter or build after every edit), hooks that inject large output into context, hooks that silently fail, and hooks with side effects outside the repository.

### Candidates to ADD

Only for evidenced, deterministic, cheap behaviours. Specify event, matcher, command, expected runtime, failure mode, and how it is tested. Prefer non-blocking unless correctness or safety requires blocking.

## Part B — MCP servers

### Inventory

For each server (every scope, including plugin-provided): name, transport, scope, shareable or personal, what it provides, number of tools, whether it starts successfully, and whether it requires credentials (do not display them).

### Evaluate

| Field | Content |
|-------|---------|
| Usage frequency | Calls per session and sessions using it, from history; zero-use servers are common |
| Usefulness | Did results change outcomes, or were they ignored? |
| Tool count | Number of tools exposed |
| Definition overhead | Approximate tokens its tool definitions add to every session (use the product's context reporting if available; otherwise estimate and label it) |
| Response size | Typical and maximum result sizes; truncation behaviour |
| Duplication | Overlap with built-in tools, other servers, Skills or scripts |
| Output quality | Accuracy, determinism, noise |
| Loading | Whether it can be enabled lazily, per scope, or per task in this version |
| Cheaper alternative | Would a script, Skill, or built-in tool do? |
| Verdict | KEEP / CHANGE (reduce tools, narrow scope, enable on demand) / REMOVE / ADD |

Emphasize a **small tool surface**. If a server exposes many tools but only a few are used, recommend restricting or replacing it where the current version supports that.

### Candidates to ADD

Only where history shows repeated retrieval pain that scripts and built-in tools handle poorly. Note that custom code-intelligence servers are out of scope for this phase; record such needs for prompt 07's "requirements for a later phase".

## Part C — Security and portability

- permissions interplay (which tools are pre-approved, any overly broad allow rules)
- anything that sends data to external services
- configuration that would break or leak when shared with other developers
- Windows/macOS/Linux compatibility of hook commands and server launchers
  (on native Windows check the shell used to run hooks, `.sh` scripts, launcher wrappers such as `cmd /c`, quoting rules, and CRLF line endings)

## Output

Write `<AUDIT_DIR>/06-hooks-and-mcps.md`:

1. Platform facts for this version (hook events, MCP loading behaviour)
2. Hook inventory, evaluation and verdicts
3. Hook candidates to add
4. MCP inventory, evaluation and verdicts
5. MCP candidates to add (if any)
6. Total estimated always-on overhead from tool definitions, before and after recommendations
7. Security/portability findings
8. Open questions

Print a summary (at most 35 lines) in chat.
