# Subagents — Claude's Notes

> Course notes: [markdown/subagents.md](../markdown/subagents.md)
> Example agent: [claude/agents/DocsExplorer.md](../claude/agents/DocsExplorer.md)

## The Core Insight: Context Isolation

The main reason subagents exist is **context isolation**. When the main Claude agent needs to look up documentation for 5 libraries, reading all of that into the main context window would:
1. Consume thousands of tokens of context budget
2. Potentially distract the main agent with irrelevant details
3. Leave less room for the actual task

A subagent does the work and returns only the **summary/result** to the main agent. The raw tool calls and intermediate steps never enter the main context.

## How It Works

```
Main Agent (you talk to this one)
  │
  ├── Spawns DocsExplorer subagent
  │       ├── Calls Context7 MCP
  │       ├── Calls WebSearch
  │       └── Returns: "Here are the relevant API docs for Bun SQLite..."
  │
  └── Receives summary, continues the task
```

The main agent only sees the final output of the subagent — not the 200 tokens of raw API documentation it fetched.

## Creating a Custom Subagent

### Directory Structure

```
.claude/
  agents/
    MyAgentName.md    ← MUST be inside .claude/agents/ (not claude/agents/)
```

> **Note**: The course's example project uses `claude/agents/` without the dot prefix. For Claude Code to auto-discover agents, the standard path is `.claude/agents/`. Check the official docs for your version.

### The Frontmatter Spec

```markdown
---
name: DocsExplorer
description: Documentation lookup specialist. Use proactively when needing docs
             for any library, framework, or technology.
tools: WebFetch, WebSearch, Skill, MCPSearch
model: sonnet
---

[Your agent's instructions here]
```

Key fields:
- **name**: How you reference it in prompts and CLAUDE.md
- **description**: What Claude reads to decide whether to spawn this agent automatically — make it specific and trigger-worthy
- **tools**: Subset of Claude's tools this agent can use — limits blast radius
- **model**: Can use a cheaper model than the main agent (Sonnet for research, Haiku for simple lookups)

### Tool Restriction Is a Feature

Giving a subagent only `WebFetch, WebSearch` means it literally cannot edit files or run shell commands even if it wanted to. This is defensive design: a documentation-fetching agent has no business touching your filesystem.

## Making Claude Use Your Agents Automatically

Claude won't always discover your agents on its own. The solution from the course:

```markdown
# In CLAUDE.md:
Whenever working with any third-party library or something similar, you MUST
look up the official documentation to ensure you're working with up-to-date
information. Use the DocsExplorer subagent for efficient documentation lookup.
```

The `MUST` is intentional — softer wording like "consider using" often gets ignored.

## Parallel Subagent Execution

Subagents can run in parallel. The DocsExplorer example shows this explicitly — all Context7 `resolve-library-id` calls fire simultaneously, then all `query-docs` calls fire simultaneously. This makes multi-library lookups dramatically faster.

The main agent can also spawn multiple different subagents in parallel if a task has independent workstreams (e.g., fetch docs + analyze codebase at the same time).

## Starting a Session with a Specific Agent

```bash
claude --agent DocsExplorer
```

This starts an interactive session where the DocsExplorer agent is the primary agent (not a subagent). Useful for documentation-heavy sessions where you know upfront you'll be doing lots of lookups.

## Built-in vs Custom Agents

Claude Code ships with several built-in agents (like `Explore` for codebase search). Custom agents you create supplement these — they don't replace them.

Built-in agents:
- **Explore**: Read-only codebase search and symbol lookup
- Others vary by Claude Code version

Custom agents are better than built-in ones for:
- Domain-specific workflows
- Integrating with specific MCP servers
- Using a custom system prompt for a narrow task

## The Description is Critical

The `description` field is what the main Claude agent reads when deciding whether to spawn your subagent. A vague description leads to under-use; an over-broad description leads to unnecessary spawning.

Good: "Fetches current library docs via Context7 and web search. Use for any question about third-party library APIs, configuration, or best practices."

Bad: "Documentation helper"
