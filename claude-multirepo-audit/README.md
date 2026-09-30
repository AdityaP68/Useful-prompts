# claude-multirepo-audit

A reusable prompt for auditing an existing multi-repository Claude Code development environment. It produces recommendations for a small, context-efficient Claude Code configuration (CLAUDE.md files, skills, documentation layout).

## Usage

1. Clone or download this repository.
2. Copy `PROMPT.md` into, or reference it from, the parent directory that contains the repositories to audit.
3. Launch Claude Code from that parent directory.
4. Ask Claude to follow `PROMPT.md`.
5. The audit is read-only. It should not modify the repositories.

## Before you run it

Review the prompt and adjust it to your workspace first. In particular, edit the "Workspace exclusions" section (backup copies, helper directories) and the repository counts to match your layout.
