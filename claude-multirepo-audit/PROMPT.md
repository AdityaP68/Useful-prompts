# Claude Code Multi-Repo Configuration Audit

You are operating from the parent directory of a frontend workspace containing 6 canonical Git repositories:

- 4 microfrontends
- 2 shared UI libraries

Developers use Claude Code in two ways:

1. From this parent workspace for tasks/features that can span multiple repositories.
2. From inside an individual repository for repository-specific work.

Audit the current workspace and existing Claude Code setup and recommend the smallest, most effective and context-efficient Claude Code configuration for this environment.

## Critical rule

DO NOT MODIFY ANYTHING.

Do not create, edit, move, rename, consolidate or delete files.

This phase is investigation and recommendations only.

## Workspace exclusions

The parent directory may contain temporary or backup copies of repositories.

These are NOT canonical application repositories.

Do not:
- treat them as repositories in the application architecture
- analyze their Claude configuration
- use them to infer coding conventions
- include their source code in dependency analysis
- count duplicated files from them
- use them when determining cross-repository relationships

There may also be a `startup/` directory containing local helper material.

It is NOT part of the application architecture.

Exclude it unless a canonical repository explicitly depends on something inside it.

The real application consists of exactly:
- 4 MFEs
- 2 shared UI libraries

If you cannot confidently determine which directories are canonical, report the ambiguity instead of guessing.

## 1. Discover the workspace

Identify the six canonical repositories.

For each determine:
- purpose
- MFE vs UI library
- technology stack
- build tooling
- package manager
- test tooling
- major entry points
- important directories
- how it is built/served
- important dependencies

Do not recursively read the entire source tree.

Prefer manifests, configuration, entry points, READMEs, existing documentation and targeted representative source inspection.

Produce a concise workspace map.

## 2. Determine cross-repository architecture

Determine how the six repositories actually interact.

Investigate:
- MFE composition/loading
- shared UI libraries
- package dependencies
- cross-MFE communication
- events
- shared contracts/types
- asset manifests
- configuration
- runtime integration
- build/deployment assumptions

Identify which repositories depend on or consume which others.

Identify common categories of changes that naturally span repositories.

Verify important relationships from code/configuration rather than inferring them from naming.

## 3. Audit existing Claude Code configuration

Find relevant:
- CLAUDE.md files
- `.claude/` directories
- settings
- skills
- commands
- agents
- hooks
- MCP configuration
- permissions
- other Claude-specific configuration

For every relevant item determine:
- purpose
- usefulness
- workspace vs repository scope
- unnecessary always-on context
- duplication
- stale information
- misplaced information

Classify existing items as:

KEEP
CHANGE
MOVE
CONSOLIDATE
REMOVE/ARCHIVE
NEEDS VERIFICATION

Do not perform these actions.

## 4. Audit existing docs

The repositories may contain accumulated Markdown documentation including:
- bug investigations
- repeated processes
- codebase knowledge
- architecture explanations
- design documents
- implementation notes
- troubleshooting knowledge
- historical decisions

The documentation may have grown organically and become fragmented.

Do NOT assume documentation is correct merely because it exists.

For important claims, compare against current code/configuration where practical.

Analyze:
- duplication
- overlapping topics
- stale information
- contradictions
- obsolete implementation details
- useful architectural knowledge
- recurring lessons
- historical-only information
- repeated procedures
- information already cheaply discoverable from code

Do not recommend combining documents merely to reduce file count.

Classify documents or logical document groups as:

KEEP
CONSOLIDATE
RESTRUCTURE
REFERENCE
ARCHIVE
STALE
VERIFY

Identify valuable durable knowledge buried inside historical bug/process documents.

## 5. Classify knowledge by consumption model

Classify useful information into:

A. Always-on workspace knowledge

B. Always-on repository knowledge

C. Task-specific knowledge

D. Reusable workflow/procedure

E. Detailed project documentation

F. Historical knowledge

G. Cheaply discoverable information that should not be permanently documented for Claude

Be aggressive about preventing unnecessary always-on context.

## 6. Evaluate context efficiency

Identify where Claude is likely wasting context or repeatedly rediscovering information.

Look for:
- oversized CLAUDE.md files
- duplicated instructions
- duplicated documentation
- repeatedly rediscovered architecture
- unnecessary documentation loaded for unrelated tasks
- broad/poorly scoped skills
- obsolete generated documentation
- information better kept in normal docs
- information trivial to discover from code

Optimize for progressive disclosure:

Claude should begin with the smallest useful context and obtain deeper information only when the task requires it.

## 7. Workspace vs repository configuration

Developers may launch Claude from either:

`<workspace>/`

or:

`<workspace>/<individual-repository>/`

Determine what belongs at each level.

Evaluate:
- root/workspace CLAUDE.md
- repository CLAUDE.md files
- workspace `.claude` configuration
- repository-specific `.claude` configuration
- workspace skills
- repository-specific skills
- normal documentation referenced by Claude instructions

Avoid duplicating information between workspace and repository levels.

Repo-level Claude usage must remain effective independently.

Workspace-level Claude usage must understand cross-repository relationships without requiring all six repositories to be explored for every task.

## 8. Identify useful skills

Identify recurring engineering workflows that genuinely justify Claude Code skills.

Derive these from the actual repository and documentation rather than creating skills speculatively.

For every proposed skill explain:
- problem it solves
- evidence the workflow recurs
- workspace vs repository scope
- what belongs in SKILL.md
- which existing docs it should reference
- what should NOT be copied into the skill

Prefer a few high-value skills over many narrow skills.

## 9. Historical Claude usage

Determine whether local Claude Code history/session data is available for this workspace.

Do not dump complete historical conversations.

Determine:
- available historical data
- approximate session count
- date range
- whether tool calls/commands are recorded
- whether model/token/cost information exists
- whether sessions can be associated with these repositories

If usable, analyze patterns such as:
- repeatedly explored files/directories
- repeatedly rediscovered architecture
- repeated searches
- repeated debugging patterns
- recurring corrections
- recurring workflows
- frequently consulted documentation
- repeated unnecessary exploration

Use this only as supporting evidence.

Do not treat historical Claude-generated statements as authoritative project knowledge. Validate important conclusions against the current repository.

## 10. Recommend target structure

Based on actual findings, propose the smallest maintainable target structure.

Do not assume every repository requires identical configuration.

Show the proposed filesystem hierarchy using actual canonical repository names.

For every proposed file/directory explain why it should exist.

## 11. CLAUDE.md responsibilities

For the root CLAUDE.md and each proposed repository CLAUDE.md specify:
- what belongs there
- what should explicitly NOT be there
- approximate desired size
- which detailed documentation it should reference
- what existing information should move out

CLAUDE.md should primarily provide concise durable guidance and navigation, not become a complete architecture manual.

## 12. Documentation restructuring

Recommend how fragmented documentation should eventually be organized.

Do not restructure anything yet.

Optimize for:
- discoverability
- low duplication
- clear ownership
- freshness
- historical retention
- navigation by humans and Claude

Avoid unnecessary hierarchy.

## Final deliverables

Produce:

1. Workspace map
2. Cross-repository dependency/interaction map
3. Existing Claude configuration audit
4. Existing documentation audit
5. Stale/duplicated/conflicting knowledge findings
6. Context-efficiency findings
7. Historical Claude usage findings, if available
8. Knowledge classification
9. Workspace-vs-repository configuration recommendation
10. Proposed CLAUDE.md responsibilities
11. Proposed skills and justification
12. Proposed documentation organization
13. Exact proposed target directory tree
14. What NOT to add
15. Prioritized migration plan
16. Open questions / areas requiring human verification

For every major recommendation, state what evidence from the current workspace led to it.

Do not implement anything.

The objective is NOT to build the most sophisticated Claude Code configuration possible.

The objective is to create the smallest maintainable Claude Code knowledge/configuration structure that allows Claude to work accurately and efficiently both within individual repositories and across this six-repository MFE system, while minimizing duplicated context, stale knowledge and repeated codebase exploration.
