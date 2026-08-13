# Plugins — Claude's Notes

> Course notes: [markdown/plugins.md](../markdown/plugins.md) *(not yet written — notes pending)*

## What Plugins Are

Plugins extend Claude Code's capabilities beyond what built-in tools, MCP servers, and skills provide. While MCP adds external data sources and skills add behavioral instructions, plugins can add entirely new tools that Claude can call — implemented as code you write and register.

Think of the difference:
- **MCP servers**: Connect Claude to external services (databases, APIs, platforms)
- **Skills**: Give Claude domain-specific instructions and best practices
- **Plugins**: Add new callable tools to Claude's toolset

## Plugin vs MCP: When to Choose Which

| | Plugins | MCP Servers |
|--|---------|-------------|
| **Protocol** | Claude Code-specific | Language-agnostic open standard |
| **Best for** | Claude Code-specific integrations | Reusable across AI tools |
| **Distribution** | Via Claude Code plugin system | Any MCP-compatible AI |
| **Complexity** | Lower (Claude Code handles hosting) | Requires running a server process |

If you're building something only for Claude Code, a plugin is simpler. If you want the same tool available in other AI tools (Claude.ai, other MCP clients), build an MCP server instead.

## Plugin Categories

Claude Code plugins typically fall into these categories:

### Tool Plugins
Add new tools Claude can call:
```
Tool: run-playwright-test
Description: Run a specific Playwright test file and return results
Input: { file: string, testName?: string }
Output: { passed: boolean, output: string, duration: number }
```

### Resource Plugins
Expose data sources Claude can read:
- Internal wikis or documentation
- Company databases (read-only)
- Build system output

### UI Plugins
Extend the Claude Code terminal interface:
- Custom status line segments
- Output formatters
- Integration with external dashboards

## Third-Party Plugin Ecosystem

Claude Code has a growing ecosystem of community plugins. Check the official Claude Code documentation for:
- The official plugin registry
- Plugin installation commands
- Plugin authoring guides

The course mentions this section for future coverage — check `markdown/plugins.md` when the instructor's notes are added.

## Security Model for Plugins

Plugins run with the same trust level as Claude Code itself. This means:
- **Vet plugins carefully** before installing — they can access your filesystem and network
- **Prefer plugins from known publishers** — the official registry has some vetting
- **Review plugin source code** for anything with sensitive system access

Unlike MCP (which runs as a separate process with its own permissions), plugins may run in the same process as Claude Code.

## Building a Plugin: The Pattern

A basic Claude Code plugin structure:

```javascript
// .claude/plugins/my-plugin/index.js
module.exports = {
  name: "my-plugin",
  version: "1.0.0",
  tools: [
    {
      name: "run-tests",
      description: "Run the project test suite and return results",
      inputSchema: {
        type: "object",
        properties: {
          filter: { type: "string", description: "Test name filter" }
        }
      },
      execute: async ({ filter }) => {
        // your implementation
        const result = await runTests(filter);
        return { success: result.exitCode === 0, output: result.stdout };
      }
    }
  ]
};
```

Claude can then call `run-tests` as a first-class tool alongside built-in tools like `Read` and `Write`.

## Practical Plugin Ideas

- **`run-e2e-tests`**: Run Playwright/Cypress and return structured results
- **`deploy-preview`**: Deploy a preview and return the URL for Claude to verify
- **`check-bundle-size`**: Analyze webpack/esbuild output and report size regressions
- **`query-analytics`**: Run specific analytics queries to inform product decisions
- **`sync-types`**: Generate TypeScript types from your OpenAPI spec or database schema

These represent tasks that currently require manual steps but could be integrated directly into Claude Code's workflow.
