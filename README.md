# Claude Code — The Practical Guide

Study notes and reference material for the Udemy course **"Claude Code — The Practical Guide"** by Maximilian Schwarzmüller.

This repository has two layers of content:
- **[Course Notes](course-notes.md)** (`markdown/`) — notes taken during the course, written by the student
- **[Claude's Notes](#claudes-notes)** (`Claude Notes/`) — deeper explanations, insights, and practical patterns written by Claude

---

## Table of Contents

### Getting Started
- [Setup](#setup)
- [Configuration](#configuration)
- [Base Usage](#base-usage)

### Key Features & Efficient Usage
- [Context Management](#context-management)
- [Memory & Instructions](#memory--instructions)
- [MCP (Model Context Protocol)](#mcp-model-context-protocol)
- [Subagents](#subagents)
- [Skills & Rules](#skills--rules)

### Beyond Local CLI Usage
- [Hooks](#hooks)
- [Plugins](#plugins)
- [Patterns & Use Cases](#patterns--use-cases)

---

## Getting Started

### Setup

**Course notes**: [markdown/setup.md](markdown/setup.md) | **Claude's notes**: [Claude Notes/01-setup.md](Claude%20Notes/01-setup.md)

Claude Code is a CLI that runs in your terminal and communicates with the Claude API through a built-in agentic loop. Installation is simple — the depth is in how you control it.

Key points:
- **Multiline prompts**: Use `Ctrl+J` or `\` + Enter. The exact shortcut varies by terminal and platform — check the [official docs](https://code.claude.com/docs/en/interactive-mode) for your setup.
- **Bun runtime**: The course uses Bun (instead of Node.js) for its built-in SQLite and direct TypeScript execution.
- **Running modes**: Interactive REPL (`claude`), one-shot (`claude -p "prompt"`), resume session (`claude -c`), or with a custom agent (`claude --agent AgentName`).

> Claude Code is fundamentally an API client that gives Claude access to your filesystem and terminal through tools. Everything else is built on top of that.

---

### Configuration

**Course notes**: [markdown/configuration.md](markdown/configuration.md) | **Claude's notes**: [Claude Notes/02-configuration.md](Claude%20Notes/02-configuration.md)

Configuration follows a three-layer hierarchy, each overriding the previous:

```
~/.claude/settings.json          ← Global (all projects)
.claude/settings.json            ← Project (committed, team-shared)
.claude/settings.local.json      ← Local (not committed, personal)
```

**Permissions** are the most important configuration. Press `SHIFT+TAB` to cycle between modes:

| Mode | What's Allowed |
|------|---------------|
| Default | Read freely, ask before writes/commands |
| Write Permissions | Edit files without asking |
| Plan | Plan only — no code changes |

The `--dangerously-skip-permissions` flag bypasses all confirmations. Only use it inside a Docker sandbox:
```bash
docker sandbox run claude
```

**Model selection**: Use `/model` or `Alt+P`. Opus for complex tasks, Sonnet for daily work, Haiku for simple edits or CI pipelines.

**Compaction**: When the context window fills, Claude auto-compacts (lossy compression). Prevent information loss by starting new sessions for distinct tasks (`/clear`) and keeping frequently-needed rules in `CLAUDE.md`.

---

### Base Usage

**Course notes**: [markdown/base-usage.md](markdown/base-usage.md) | **Claude's notes**: [Claude Notes/03-base-usage.md](Claude%20Notes/03-base-usage.md)

#### Essential Commands

| Command | Description |
|---------|-------------|
| `/clear` | Start fresh context (new session) |
| `/compact` | Summarize + clear context |
| `/model` | Switch AI model |
| `/init` | Generate `CLAUDE.md` from codebase scan |
| `/rewind` | Undo last action (prefer git instead) |
| `/permissions` | View/change permissions |
| `/context` | Check context window usage |
| `/usage` | View plan usage and remaining quota |

#### Essential Shortcuts

| Shortcut | Action |
|----------|--------|
| `SHIFT+TAB` | Cycle permission modes |
| `CTRL+C` | Cancel current input/generation |
| `ESC` | Interrupt generation (inject new prompt) |
| `ESC+ESC` | Undo last action |
| `ALT+P` | Switch model |
| `CTRL+O` | Toggle verbose output |
| `CTRL+B` | Move task to background |

#### Plan Mode
Use Plan Mode for virtually all non-trivial tasks. It forces Claude to describe its intended changes before making them, catching wrong assumptions early. Toggle with `SHIFT+TAB` or start with `--permission-mode plan`.

#### Version Control is Your Safety Net
`/rewind` is unreliable. Always commit before starting a significant Claude task. If Claude produces a bad result, `git checkout` is the reliable rollback.

---

## Key Features & Efficient Usage

### Context Management

**Course notes**: [markdown/context-management.md](markdown/context-management.md) | **Claude's notes**: [Claude Notes/04-context-management.md](Claude%20Notes/04-context-management.md)

Context is a shared budget — everything Claude reads, writes, and reasons about consumes it. Good context management is the difference between a productive session and a confusing one.

#### The SPEC.md → CLAUDE.md Workflow

1. **Draft a SPEC.md** with another AI (ChatGPT, Gemini) — describe tech stack, data model, auth, user flows
2. **Initialize the project** manually (install deps yourself — AI sometimes only edits `package.json`)
3. **Run `/init`** in a new Claude session — reads all files including SPEC.md and generates `CLAUDE.md`
4. **Reference `@SPEC.md`** in future prompts whenever architectural context is needed

#### Prompt Engineering Principles

- **Concise & precise**: More signal, less noise. Every unnecessary token wastes context budget.
- **Reference files you know matter**: Use `@filename` deliberately — pointing at irrelevant files is counterproductive.
- **Think → Plan → Prompt**: Plan before typing. Excessive follow-ups usually mean the initial prompt needed more thought.
- **Don't hide challenges**: If you know a pitfall exists, include it in the prompt with a recommended approach.
- **Explicitly name tools**: Tell Claude to "use the DocsExplorer subagent" or "use Context7" — don't hope it chooses the right tool automatically.

---

### Memory & Instructions

**Course notes**: [markdown/memory-and-instructions.md](markdown/memory-and-instructions.md) *(empty)* / [markdown/claude-md-file.md](markdown/claude-md-file.md) | **Claude's notes**: [Claude Notes/05-memory-and-instructions.md](Claude%20Notes/05-memory-and-instructions.md)

Claude Code has two memory systems:

#### CLAUDE.md
Maintained by you. Loaded every session. Contains permanent project rules and context.

```markdown
# Example CLAUDE.md
We're building the app described in @SPEC.md.

Keep replies concise. No fluff, no long explanatory code snippets.

Whenever working with any third-party library, use the DocsExplorer
subagent for efficient documentation lookup.

Never modify files in /migrations — only create new migration files.
```

- Can be nested in subdirectories (Claude reads the closest one to the files it's working on)
- Keep it focused — it costs context budget every session
- Reference `@SPEC.md` here so architectural context is always available

#### Auto Memory (since v2.1.59)
Claude Code autonomously stores learned behaviors in `~/.claude/projects/<project>/memory/`. It learns from corrections you give repeatedly and from things you call out consistently.

- Use `/memory` to browse, edit, or delete memory entries
- It supplements CLAUDE.md — it doesn't replace intentional instructions
- If Claude behaves unexpectedly, check the memory files for stale or incorrect entries

---

### MCP (Model Context Protocol)

**Course notes**: [markdown/mcp.md](markdown/mcp.md) | **Claude's notes**: [Claude Notes/06-mcp.md](Claude%20Notes/06-mcp.md)

MCP is an open standard that connects Claude to external tools and data sources — databases, APIs, documentation, Slack, GitHub, and more.

```bash
# Add an MCP server
claude mcp add <server-name>

# Add globally (all projects)
claude mcp add --scope user <server-name>

# Manage MCP in-session
/mcp
```

**Context7** is the most useful MCP for development — it gives Claude access to current library documentation, preventing Claude from using stale training data for rapidly-evolving APIs.

Without MCP, Claude is limited to training data (potentially outdated) and files on your filesystem. With MCP, it can access live external systems.

> Security: vet MCP servers carefully — they can have broad system access. Use permission controls in `settings.json` to restrict which tools are allowed.

---

### Subagents

**Course notes**: [markdown/subagents.md](markdown/subagents.md) | **Claude's notes**: [Claude Notes/07-subagents.md](Claude%20Notes/07-subagents.md)

Subagents are specialized agents that run in parallel with the main agent, performing delegated tasks. The main agent only sees the subagent's **output** — not the full tool call history — keeping the main context clean.

#### Creating a Custom Subagent

1. Create `.claude/agents/` directory (**name is mandatory**)
2. Create `AgentName.md` with frontmatter:

```markdown
---
name: DocsExplorer
description: Documentation lookup specialist. Use proactively when needing docs
             for any library, framework, or technology.
tools: WebFetch, WebSearch, Skill, MCPSearch
model: sonnet
---

[Agent instructions]
```

The `description` is what Claude reads to decide whether to spawn the agent automatically — write it with trigger conditions in mind.

#### Encouraging Agent Use

Add explicit instructions to `CLAUDE.md`:
```markdown
Whenever working with any third-party library, you MUST look up the official
documentation. Use the DocsExplorer subagent for efficient documentation lookup.
```

#### Starting a Session as an Agent
```bash
claude --agent DocsExplorer  # Start interactive session with this agent as the primary
```

See the working example: [claude/agents/DocsExplorer.md](claude/agents/DocsExplorer.md)

---

### Skills & Rules

**Course notes**: [markdown/skills-and-rules.md](markdown/skills-and-rules.md) | **Claude's notes**: [Claude Notes/08-skills-and-rules.md](Claude%20Notes/08-skills-and-rules.md)

Skills are dynamically-loaded instruction sets for specific domains. Unlike `CLAUDE.md` (always loaded), skills are loaded on demand — either auto-detected by Claude or invoked explicitly via slash commands.

#### Creating a Skill

```
.claude/
  skills/
    skill-name/         ← folder name = slash command
      SKILL.md
      references/       ← optional deep-dive material
        extra-info.md
```

```markdown
---
name: skill-name
description: When and why to use this skill — be specific for auto-discovery
allowed-tools: Read
---

[Skill instructions]
```

#### Useful Frontmatter Options

| Option | Effect |
|--------|--------|
| `allowed-tools: Read` | Restrict what Claude can do during this skill |
| `disable-model-invocation: true` | Slash command only — no auto-invocation |
| `user-invocable: false` | Auto-invocation only — no slash command |

#### The $ARGUMENTS Pattern

```markdown
---
name: code-review
description: Review code for bugs, security, or performance issues.
allowed-tools: Read
---

MODE: $ARGUMENTS

- MODE == BUGS: Focus only on bugs
- MODE == SECURITY: Focus only on security
- (default): General review
```

Usage: `/code-review SECURITY`, `/code-review BUGS`

#### Skills in This Repository

| Skill | Purpose |
|-------|---------|
| [modern-best-practice-react-components](claude/skills/modern-best-practice-react-components/SKILL.md) | React 19+ best practices |
| [modern-best-practice-nextjs](claude/skills/modern-best-practice-nextjs/SKILL.md) | Next.js App Router patterns |
| [clean-typescript](claude/skills/clean-typescript/SKILL.md) | TypeScript style and safety |
| [modern-accessible-html-jsx](claude/skills/modern-accessible-html-jsx/SKILL.md) | Accessibility for JSX |
| [modern-browser-apis](claude/skills/modern-browser-apis/SKILL.md) | Modern browser API usage |
| [modern-tailwind](claude/skills/modern-tailwind/SKILL.md) | Tailwind CSS v4 patterns |
| [web-security](claude/skills/web-security/SKILL.md) | Web security checklist |
| [bun-first](claude/skills/bun-first/SKILL.md) | Bun-specific APIs and idioms |

Third-party skills are available at [skills.sh](https://www.skills.sh/) — install with `npx skills add <owner/repo>`.

---

## Beyond Local CLI Usage

### Hooks

**Course notes**: [markdown/hooks.md](markdown/hooks.md) *(not yet written)* | **Claude's notes**: [Claude Notes/09-hooks.md](Claude%20Notes/09-hooks.md)

Hooks are shell commands that the Claude Code harness runs automatically at lifecycle events. They're configured in `settings.json` and are **deterministic** — they always run, regardless of AI judgment.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write",
      "hooks": [{ "type": "command", "command": "npx tsc --noEmit" }]
    }],
    "Stop": [{
      "hooks": [{ "type": "command", "command": "notify-send 'Claude done'" }]
    }]
  }
}
```

**Hook events**: `PreToolUse`, `PostToolUse`, `Notification`, `Stop`

Hook output is visible to Claude — this creates a self-correcting feedback loop (e.g., TypeScript errors after a file write go back to Claude, which then self-corrects).

> Use hooks for **guarantees** (things that must always happen). Use `CLAUDE.md` instructions for **guidance** (things Claude should usually do).

---

### Plugins

**Course notes**: [markdown/plugins.md](markdown/plugins.md) *(not yet written)* | **Claude's notes**: [Claude Notes/10-plugins.md](Claude%20Notes/10-plugins.md)

Plugins extend Claude Code with custom tools beyond what built-in tools, MCP, and skills provide. They allow you to add first-class callable tools implemented as code.

- **vs MCP**: Plugins are Claude Code-specific; MCP is an open standard reusable across AI tools
- **vs Skills**: Plugins add new callable tools; skills add instructional content
- **Security**: Plugins run with the same trust level as Claude Code — vet them carefully

See [Claude Notes/10-plugins.md](Claude%20Notes/10-plugins.md) for implementation details and practical use cases.

---

### Patterns & Use Cases

**Course notes**: [markdown/patterns-and-use-cases.md](markdown/patterns-and-use-cases.md) *(not yet written)* | **Claude's notes**: [Claude Notes/11-patterns-and-use-cases.md](Claude%20Notes/11-patterns-and-use-cases.md)

Key patterns for getting the most out of Claude Code:

| Pattern | When to Use |
|---------|------------|
| **SPEC-First Greenfield** | Starting a new project |
| **Focused Task Session** | Adding features or fixing bugs |
| **Documentation-First Integration** | Working with unfamiliar or recently-changed libraries |
| **Iterative Refinement Loop** | Tuning results to match your intent |
| **Code Review Session** | Auditing finished work (read-only mode) |
| **Debugging Session** | Fresh context + Plan mode for stubborn bugs |
| **Multi-Agent Parallelism** | Tasks with independent workstreams |

**Anti-patterns to avoid**:
- Long sprawling sessions with multiple unrelated features
- Vague corrections ("that's wrong, try again")
- Accepting output without reviewing the diff
- Hiding known pitfalls from Claude to "test" it
- Monolithic prompts for large features

See [Claude Notes/11-patterns-and-use-cases.md](Claude%20Notes/11-patterns-and-use-cases.md) for full details including CI/CD integration, documentation generation, and test generation patterns.

---

## Claude's Notes

Deep-dive notes written by Claude on each topic — covering the "why", practical tips, and insights beyond what the course covers directly.

| Topic | File |
|-------|------|
| Setup | [Claude Notes/01-setup.md](Claude%20Notes/01-setup.md) |
| Configuration | [Claude Notes/02-configuration.md](Claude%20Notes/02-configuration.md) |
| Base Usage | [Claude Notes/03-base-usage.md](Claude%20Notes/03-base-usage.md) |
| Context Management | [Claude Notes/04-context-management.md](Claude%20Notes/04-context-management.md) |
| Memory & Instructions | [Claude Notes/05-memory-and-instructions.md](Claude%20Notes/05-memory-and-instructions.md) |
| MCP | [Claude Notes/06-mcp.md](Claude%20Notes/06-mcp.md) |
| Subagents | [Claude Notes/07-subagents.md](Claude%20Notes/07-subagents.md) |
| Skills & Rules | [Claude Notes/08-skills-and-rules.md](Claude%20Notes/08-skills-and-rules.md) |
| Hooks | [Claude Notes/09-hooks.md](Claude%20Notes/09-hooks.md) |
| Plugins | [Claude Notes/10-plugins.md](Claude%20Notes/10-plugins.md) |
| Patterns & Use Cases | [Claude Notes/11-patterns-and-use-cases.md](Claude%20Notes/11-patterns-and-use-cases.md) |

---

## Resources

- [Official Claude Code Documentation](https://code.claude.com/docs/en/overview)
- [CLI Reference](https://code.claude.com/docs/en/cli-reference)
- [Interactive Mode & Shortcuts](https://code.claude.com/docs/en/interactive-mode)
- [Settings Reference](https://code.claude.com/docs/en/settings)
- [Tools Reference](https://code.claude.com/docs/en/tools-reference)
- [Third-Party Skills Repository](https://www.skills.sh/)
