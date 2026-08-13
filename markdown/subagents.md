# SUBAGENTS

Subagents are special agents managed by Claude Code to delegate tasks out of the main agent.

It works in paralel with the main agent, using its specific skill: for example, `explore` subagent does the web research.

**The main goal is to delegate side works so that the main context won't be poluted!** It uses, however, an amout of token, but it isn't included on Claude's main context - instead, Claude Code will consume just the output of a subagent (the result of its job).

# CREATING AND USING A CUSTOM SUBAGENT

When it comes to reading a documentation in the main agent (such as Context7 does), it can be tricky, because it's a lot of text going on in the main window. That's why you can build a custom subagent.

## STEPS
- Create a folder called `agents`. **THE NAME `agents` IS MANDATORY**
- Create a file with a name that best describes your subagent task: [!DocsExplorer.md](/claude/agents/DocsExplorer.md)

### THE .md FILE
```md
---
name: DocsExplorer
description: Documentation lookup specialist. Use proactively when needing docs for any library, framework, or technology. Fetches docs in parallel for multiple technologies.
tools: WebFetch, WebSearch, Skill, MCPSearch
model: sonnet
---
```

You can find more tools in [Claude's the official documentation](https://code.claude.com/docs/en/tools-reference#configure-tools-with-permission-rules-and-hooks)

You don't need to be technical, just give instructions. Ask another AI to create a better description for a specific custom subagent.

# ENCOURAGING AGENT USAGE

Instead of "hoping" that Claude Code sees your custom subagent, you can ensure that it uses by adding the information in `CLAUDE.md` file.

```md
Whenever working with any third-party library or something similar, you MUST look up the official documentation to ensure that you're working with up-to-date information.
Use the DocsExplorer subagent for efficient documentation lookup.
```

