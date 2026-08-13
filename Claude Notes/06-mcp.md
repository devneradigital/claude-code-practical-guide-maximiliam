# MCP (Model Context Protocol) — Claude's Notes

> Course notes: [markdown/mcp.md](../markdown/mcp.md)

## What MCP Actually Is

MCP (Model Context Protocol) is an open standard for connecting AI models to external tools and data sources. Think of it as a plugin system for AI — instead of Claude only knowing what's in its training data or what you paste into the prompt, MCP servers give it live access to external systems.

Each MCP server exposes:
- **Tools** — actions Claude can call (e.g., query a database, search Slack, fetch docs)
- **Resources** — structured data sources Claude can read

## Why MCP Matters More Than It Seems

Without MCP, Claude's knowledge is bounded by:
1. Training data (potentially outdated)
2. What you paste into the context window
3. Files in your filesystem (via built-in tools)

With MCP, Claude can:
- Fetch current library documentation (Context7)
- Query your production database for schema info
- Search your team's Slack or Notion
- Call GitHub APIs to understand PR context
- Run queries against monitoring systems

This transforms Claude Code from a code editor into an actual system that understands your entire development environment.

## Installation

```bash
# Add an MCP server to Claude Code
claude mcp add <server-name>

# Add globally (available across all projects)
claude mcp add --scope user <server-name>

# View installed MCP servers
/mcp  # inside a session
```

## Context7: The Most Useful MCP for Development

The course's primary MCP example is Context7, which gives Claude access to up-to-date documentation for libraries and frameworks. This solves a real problem: Claude's training data has a cutoff, and APIs change.

**Without Context7**: Claude might use a deprecated Next.js App Router pattern from 6 months ago.

**With Context7**: Claude fetches current docs and generates code that matches the current API.

The DocsExplorer subagent (see `claude/agents/DocsExplorer.md`) is built to use Context7 as its primary lookup mechanism, with web search as fallback.

## Security Considerations

MCP servers run as separate processes and can have significant access to your systems. When adding an MCP server:

1. **Trust the source** — only install MCP servers from reputable providers
2. **Review what tools it exposes** — use `/mcp` to see what capabilities are registered
3. **Use permission controls** — you can restrict which MCP tools Claude can call via `settings.json`

A poorly-configured MCP server for your production database could allow Claude to run destructive queries. Scope access appropriately.

## Scope: User vs Project

- `--scope user` (global): Available in every project on this machine. Good for general-purpose servers like Context7 or GitHub.
- Default (project): Only available in the current project. Good for project-specific servers (internal APIs, specific databases).

## Custom MCP Servers

You can build your own MCP server for internal tools. The protocol is well-documented, and there are SDKs for Node.js, Python, and other languages. Use cases:

- Internal API explorer for your product
- Company knowledge base search
- Legacy system adapter
- Custom monitoring or logging tools

For a team, a shared MCP server checked into the project config (`.claude/settings.json`) ensures everyone has the same tools available.

## MCP vs Built-in Tools

| | MCP Tools | Built-in Tools |
|--|-----------|---------------|
| **Examples** | Context7, GitHub, Slack | Bash, Read, Write, WebFetch |
| **Scope** | You install them | Always available |
| **Customizable** | Yes — build your own | No |
| **Permissions** | Per-tool in settings | Yes, via allowedTools |

MCP tools supplement built-in tools — they don't replace them.
