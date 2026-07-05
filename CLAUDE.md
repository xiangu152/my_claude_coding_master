<!-- OMC:START -->
<!-- OMC:VERSION:4.15.2 -->

# oh-my-claudecode - Intelligent Multi-Agent Orchestration

You are running with oh-my-claudecode (OMC), a multi-agent orchestration layer for Claude Code.
Coordinate specialized agents, tools, and skills so work is completed accurately and efficiently.

<operating_principles>
- Delegate specialized work to the most appropriate agent.
- Prefer evidence over assumptions: verify outcomes before final claims.
- Choose the lightest-weight path that preserves quality.
- Consult official docs before implementing with SDKs/frameworks/APIs.
</operating_principles>

<delegation_rules>
Delegate for: multi-file changes, refactors, debugging, reviews, planning, research, verification.
Work directly for: trivial ops, small clarifications, single commands.
Route code to `executor` (use `model=opus` for complex work). Uncertain SDK usage → `document-specialist` (repo docs first; Context Hub / `chub` when available, graceful web fallback otherwise).
</delegation_rules>

<model_routing>
`haiku` (quick lookups), `sonnet` (standard), `opus` (architecture, deep analysis).
Direct writes OK for: `~/.claude/**`, `.omc/**`, `.claude/**`, `CLAUDE.md`, `AGENTS.md`.
</model_routing>

<skills>
Invoke via `/oh-my-claudecode:<name>`. Trigger patterns auto-detect keywords.
Tier-0 workflows include `autopilot`, `ultrawork`, `ralph`, `team`, and `ralplan`.
Keyword triggers: `"autopilot"→autopilot`, `"ralph"→ralph`, `"ulw"→ultrawork`, `"ccg"→ccg`, `"ralplan"→ralplan`, `"deep interview"→deep-interview`, `"deslop"`/`"anti-slop"`→ai-slop-cleaner, `"deep-analyze"`→analysis mode, `"tdd"`→TDD mode, `"deepsearch"`→codebase search, `"ultrathink"`→deep reasoning, `"cancelomc"`→cancel.
Team orchestration is explicit via `/team`.
Detailed agent catalog, tools, team pipeline, commit protocol, and full skills registry live in the native `omc-reference` skill when skills are available, including reference for `explore`, `planner`, `architect`, `executor`, `designer`, and `writer`; this file remains sufficient without skill support.
</skills>

<verification>
Verify before claiming completion. Size appropriately: small→haiku, standard→sonnet, large/security→opus.
If verification fails, keep iterating.
</verification>

<failure_mode_guards>
User input: when clarification, preference, or approval is required and AskUserQuestion is available, use AskUserQuestion instead of ending with a prose question; ask one focused question with 2-4 options. Use prose only when AskUserQuestion is unavailable or a free-form value is required.
Session/worktree continuity: before editing after resume/compaction or inside a linked worktree, re-check `git status --short --branch`, current cwd, and relevant `.omc/state/` or `.omc/handoffs/` artifacts so work does not continue on the wrong branch or stale context.
No fake completion: TODO-style placeholder notes, `test.skip`/`.only`, stub tests, and unimplemented branches are blockers, not evidence. Before completion, inspect changed files for these patterns and either implement them or report the blocker explicitly.
</failure_mode_guards>

<execution_protocols>
Broad requests: explore first, then plan. 2+ independent tasks in parallel. `run_in_background` for builds/tests.
Keep authoring and review as separate passes: writer pass creates or revises content, reviewer/verifier pass evaluates it later in a separate lane.
Never self-approve in the same active context; use `code-reviewer` or `verifier` for the approval pass.
Before concluding: zero pending tasks, tests passing, verifier evidence collected.
</execution_protocols>

<hooks_and_context>
Hooks inject `<system-reminder>` tags. Key patterns: `hook success: Success` (proceed), `[MAGIC KEYWORD: ...]` (invoke skill), `The boulder never stops` (ralph/ultrawork active).
Persistence: `<remember>` (7 days), `<remember priority>` (permanent).
Kill switches: `DISABLE_OMC`, `OMC_SKIP_HOOKS` (comma-separated).
</hooks_and_context>

<cancellation>
`/oh-my-claudecode:cancel` ends execution modes. Cancel when done+verified or blocked. Don't cancel if work incomplete.
</cancellation>

<worktree_paths>
State root: `.omc/` by default, or `$OMC_STATE_DIR/{project-id}/` when `OMC_STATE_DIR` is set, or the parent `.omc/` when a `.omc-workspace` marker anchors a multi-repo workspace. Runtime state includes `.omc/state/`, `.omc/state/sessions/{sessionId}/`, `.omc/notepad.md`, `.omc/project-memory.json`, `.omc/plans/`, `.omc/research/`, `.omc/logs/`, `.omc/artifacts/`, `.omc/handoffs/`, and `.omc/ultragoal/`. These are ignored operational artifacts by default; `.omc/skills/**` is the intentional committable exception for project-scoped skills. In linked git worktrees, local `.omc/` state is removed with the worktree unless centralized via `OMC_STATE_DIR`.
</worktree_paths>

## Setup

Say "setup omc" or run `/oh-my-claudecode:omc-setup`.

## Python Projects

When working on **any Python project**, read the following files in order before writing code:

1. `~/.claude/rules/code-python-style.md` — Core coding standards (must follow)
2. `~/.claude/knowledge/python/summary_index.md` — Index and quick reference
3. `~/.claude/knowledge/python/details/*.md` — All detailed reference documents

## pytest (Testing)

When writing **tests using pytest** or **configuring pytest**, read these files:

1. `~/.claude/knowledge/python/pytest/summary_index.md` — Architecture overview, concept glossary, quick reference
2. `~/.claude/knowledge/python/pytest/details/*.md` — Complete original documentation (50 files)

Always prefer pytest fixtures over setup/teardown, use parametrize for data-driven tests, and follow src layout with importlib import mode.

## AgentScope Projects

When working on **AgentScope** projects, read the following files before writing code:

1. `~/.claude/knowledge/agent/agentscope/summary_index.md` — Architecture overview and quick reference
2. `~/.claude/knowledge/agent/agentscope/details/*.md` — Complete original documentation (16 files)

## Pydantic (Data Validation)

When writing **any Python code that handles data validation, serialization, or settings management**, use Pydantic and read these files:

1. `~/.claude/knowledge/python/pydantic/summary_index.md` — Architecture overview, concept glossary, common patterns
2. `~/.claude/knowledge/python/pydantic/details/*.md` — Complete original documentation (32 files)

Always prefer Pydantic BaseModel, Field, TypeAdapter, and BaseSettings over manual validation.

## LangChain / LangGraph (LLM Applications)

When building **LLM applications, agents, RAG systems, or multi-agent workflows**, read these files:

1. `~/.claude/knowledge/agent/langchain/summary_index.md` — Architecture overview, concept glossary, common patterns
2. `~/.claude/knowledge/agent/langchain/details/*.md` — Complete original documentation (38 files, includes LangGraph)

## Claude Agent SDK

When building **AI agents using the Claude Agent SDK**, read these files:

1. `~/.claude/knowledge/agent/claude-agent-sdk/summary_index.md` — Architecture overview, concept glossary, common patterns
2. `~/.claude/knowledge/agent/claude-agent-sdk/details/*.md` — Complete original documentation (28 files)

## FastMCP (MCP Server/Client Framework)

When building **MCP servers or clients using FastMCP**, read these files:

1. `~/.claude/knowledge/agent/mcp/fastmcp/summary_index.md` — Architecture overview, concept glossary, quick reference
2. `~/.claude/knowledge/agent/mcp/fastmcp/details/*.md` — Complete original documentation (115 files)

Use `@mcp.tool()`, `@mcp.resource()`, `@mcp.prompt()` decorators. Prefer FastMCP over raw MCP SDK.

## Redis (In-Memory Database)

When working with **Redis** (caching, pub/sub, sessions, queues, distributed locks), read these files:

1. `~/.claude/knowledge/db/redis/summary_index.md` — Architecture overview, data types, command reference
2. `~/.claude/knowledge/db/redis/details/*.md` — Complete original documentation (40 files)

Prefer Redis data structures over naive key-value patterns. Use pipelining for bulk operations.

## Git (Version Control)

When working with **Git** (version control, branching, merging, rebasing), read these files:

1. `~/.claude/knowledge/shared/git/summary_index.md` — Architecture overview, concept glossary, command reference
2. `~/.claude/knowledge/shared/git/details/*.md` — Complete original documentation (13 files, Pro Git 中文版)

Prefer `git switch` over `git checkout` for branch switching. Use `git rebase` for clean history.

## Docker (Container Platform)

When working with **Docker** (containers, images, Compose, networking, storage), read these files:

1. `~/.claude/knowledge/deployment/docker/summary_index.md` — Architecture overview, concept glossary, command reference
2. `~/.claude/knowledge/deployment/docker/details/*.md` — Complete original documentation (58 files)

Prefer multi-stage builds for smaller images. Use Docker Compose for multi-container applications.
<!-- OMC:END -->

<!-- User customizations -->
# Persistent Agent

Read and follow the persistent-agent skill at $HOME/.claude/skills/persistent-agent/SKILL.md

On every session start:
1. Read $HOME/.claude/workspace/SOUL.md
2. Read $HOME/.claude/workspace/USER.md
3. Read $HOME/.claude/workspace/MEMORY.md
4. Read $HOME/.claude/workspace/memory/$(date +%Y-%m-%d).md (today)
5. Read $HOME/.claude/workspace/memory/$(date -v-1d +%Y-%m-%d).md (yesterday)