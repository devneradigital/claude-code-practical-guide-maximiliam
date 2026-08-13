# Setup — Claude's Notes

> Course notes: [markdown/setup.md](../markdown/setup.md)

## What's Actually Happening at Install Time

Claude Code is a CLI that wraps the Claude API with a built-in agentic loop. When you run `claude`, it starts a REPL-like session where each of your messages becomes a turn in a long-running conversation that can invoke tools (Read, Write, Bash, WebFetch, etc.) autonomously.

The setup is intentionally minimal: install the npm package, authenticate once, and you're in. The complexity surfaces in **how you control it**, not how you install it.

## Platform Differences Matter More Than You'd Expect

The course warns about keyboard shortcut differences across platforms — this is worth taking seriously. The same conceptual action (entering a newline) uses:

- `Shift+Enter` on most terminals
- `Option+Enter` on macOS iTerm2
- `Ctrl+J` universally (most reliable)

If your terminal intercepts any of these, Claude Code becomes frustrating fast. The universal fallback `\` + Enter works everywhere.

## Bun vs Node

The course uses Bun. Worth knowing why Bun is interesting here:
- Built-in SQLite (no `better-sqlite3` or `@prisma/client` needed for simple apps)
- Faster install times
- Direct TypeScript execution without a build step

If you're following along, installing Bun early saves you from friction mid-course.

## Running Modes

Claude Code has three main entry points:

| Mode | Command | Use When |
|------|---------|----------|
| Interactive REPL | `claude` | Normal development work |
| One-shot | `claude -p "prompt"` | Scripting, CI pipelines |
| Resume session | `claude -c` | Picking up yesterday's work |

The `-p` (print/one-shot) mode is underrated — it's how you integrate Claude Code into shell scripts, git hooks, or automated workflows without leaving an interactive session open.

## Key Insight: Claude Code is Just an API Client

Understanding this demystifies a lot. Claude Code is not magic — it's a well-engineered client that:
1. Gives Claude access to your filesystem via tools
2. Maintains a conversation history with those tool results
3. Exposes that conversation through a terminal UI

Everything else (plans, memory, subagents, skills) is built on top of this foundation.
