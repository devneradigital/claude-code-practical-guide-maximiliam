# Context Management — Claude's Notes

> Course notes: [markdown/context-management.md](../markdown/context-management.md)

## Context is a Budget, Not Just a Log

The context window is a fixed-size memory that holds everything: your instructions, Claude's responses, every file it read, every tool result. When it fills up, compaction happens — lossy compression that discards detail.

**Key insight**: the context window is a shared resource. If you dump a 5000-line file into context for a 10-line change, you've wasted budget that could have been used for better reasoning about the actual problem.

## The SPEC.md → CLAUDE.md Workflow

This is the core workflow the course recommends for greenfield projects:

```
1. Craft a detailed SPEC.md with another AI (ChatGPT, Gemini, etc.)
   → Think: tech stack, auth model, data model, API design, key user flows

2. Open Claude Code, paste/refine the spec
   → "We're building an app described in @SPEC.md. Format this as proper markdown."

3. Initialize the project manually (npm/bun install, etc.)
   → Don't trust Claude to install deps — it sometimes only edits package.json

4. Run /init in a new Claude session
   → Claude reads all files including SPEC.md and generates CLAUDE.md

5. Every future session starts with CLAUDE.md already loaded
```

The `@SPEC.md` syntax is a file reference — Claude reads the file's content and includes it in context. Use it for any file you want Claude to have full access to without manually pasting.

## Prompt Engineering Principles

### Concise and Precise

Shorter prompts with more signal are better than longer prompts with noise. Every token you spend on unnecessary context is a token that can't be used for reasoning.

**Bad**: "Maybe you could look at the auth module and perhaps fix the bug where it sometimes doesn't work correctly on certain edge cases"

**Good**: "In `src/auth/login.ts`, the `validateToken()` function returns null instead of throwing when the JWT is expired. Fix it to throw an `AuthError` with message 'Token expired'."

### Reference Files You Know Matter

The `@filename` syntax is powerful but use it deliberately:
- `@SPEC.md` — always relevant for architecture questions
- `@src/types.ts` — always relevant when dealing with TypeScript types
- Random files you *think* might be relevant — usually noise

### Think → Plan → Prompt

The course frames this as: don't just type. The sequence:
1. **Think** about what you actually want and what constraints exist
2. **Plan** what Claude will need to know to do it correctly
3. **Prompt** with that information, including known pitfalls upfront

If you know about a gotcha, say so in the initial prompt — don't make Claude discover it after writing bad code.

### Don't Hide Challenges

If you know the hard part of a task (e.g., "the legacy auth system doesn't support async"), tell Claude upfront. This is not "testing" Claude — it's giving the contractor the building codes before they start laying foundation.

## Telling Claude Which Tools to Use

Claude has many tools available (file I/O, bash, web fetch, MCP servers, subagents). It won't always choose the best one automatically. Be explicit:

- "Use the DocsExplorer subagent to look up the Bun SQLite API"
- "Use Context7 MCP for the React docs"
- "Read `src/db/schema.sql` before making any database changes"

This prevents the common failure mode where Claude uses its training data (which may be stale) instead of fetching current documentation.

## Managing Long Sessions

When a session grows long:
- `/compact` to summarize what's happened and continue
- `/clear` to start fresh (use when switching tasks completely)
- Start a new terminal window for a genuinely new feature (avoids cross-contamination)

The `/context` command shows you how full your context window is. Use it proactively to decide when to compact.

## The `$ARGUMENTS` Pattern

Mentioned in the skills section but also relevant here: you can build prompts that accept runtime arguments. This works both in skills and in direct CLI usage:

```bash
claude "Review @$1 for security issues" # Shell variable injection
```

Combined with scripts, this creates reusable prompt templates.
