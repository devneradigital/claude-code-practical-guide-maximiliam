# Memory & Instructions — Claude's Notes

> Course notes: [markdown/memory-and-instructions.md](../markdown/memory-and-instructions.md) *(empty — topic covered across other notes)*
> Also see: [markdown/claude-md-file.md](../markdown/claude-md-file.md)

## Two Systems, Two Jobs

Claude Code has two distinct memory mechanisms that serve different purposes:

| | CLAUDE.md | Auto Memory |
|--|-----------|-------------|
| **Who writes it** | You | Claude Code |
| **What it stores** | Architectural rules, project context, permanent instructions | Learned behaviors, corrections, style preferences |
| **When it loads** | Every session | Every session (first 200 lines of MEMORY.md) |
| **Survives compaction** | Yes | Yes |
| **Where it lives** | Project root (or subfolders) | `~/.claude/projects/<project>/memory/` |

## Crafting a Good CLAUDE.md

The CLAUDE.md file is loaded into every session's system prompt. This means:
- It **costs tokens** — keep it focused
- It **shapes behavior globally** — make it durable and general
- It **can reference other files** — `@SPEC.md` inside CLAUDE.md is valid

### What to put in CLAUDE.md

**Good candidates**:
- Tech stack and architecture overview
- Code style rules that aren't obvious from the code
- Which tools to prefer (e.g., "use the DocsExplorer subagent for library lookups")
- What NOT to do (e.g., "never modify migration files, only add new ones")
- Reference to SPEC.md for architectural context

**Bad candidates**:
- Step-by-step task instructions (these belong in your prompt)
- Things that are obvious from reading the code
- Temporary notes about current work-in-progress

### Minimal Example

```markdown
We're building the app described in @SPEC.md. Read that file for general
architectural questions, database structure, and tech stack.

Keep replies concise. No fluff, no long explanatory code snippets.

Whenever working with any third-party library, use the DocsExplorer
subagent to look up current documentation.

Never modify files in `/migrations`. Only create new migration files.
```

## Nested CLAUDE.md Files

You can have multiple CLAUDE.md files in subdirectories. Claude reads the one closest to the files it's working on. This is useful for monorepos or projects with distinct domains:

```
/CLAUDE.md                          ← global project context
/packages/api/CLAUDE.md             ← API-specific rules
/packages/frontend/CLAUDE.md        ← Frontend-specific rules
```

The subdirectory CLAUDE.md supplements (doesn't replace) the root one.

## Auto Memory: What It's Good For

Auto Memory is Claude's attempt to learn from your behavior across sessions. It's most useful for:

- **Style corrections**: If you keep telling Claude "use semicolons" or "use single quotes", it eventually remembers
- **Behavioral corrections**: If a specific mistake keeps happening and you keep correcting it, Auto Memory can break the cycle
- **Project-specific vocabulary**: Technical terms, naming conventions, domain jargon

**The limit**: Auto Memory is trained on Claude's observations, not your explicit intentions. It can miss important context or pick up the wrong signal. Always check `~/.claude/projects/<project>/memory/` if Claude's behavior seems inexplicably inconsistent.

## `/memory` Command

Run `/memory` inside a session to:
- Browse all Auto Memory files for the project
- Edit specific memory entries
- Delete incorrect memories
- Disable Auto Memory entirely if you find it introducing noise

## The Hierarchy of Instruction Sources

When Claude Code decides how to behave, it consults (in order of priority):
1. Your current session prompt
2. Auto Memory (MEMORY.md)
3. CLAUDE.md (project)
4. Claude Code's default system prompt

Explicit instructions in your prompt always win. Auto Memory can be overridden session-by-session by restating a preference explicitly.

## Practical Pattern: Correction Loop

If Claude keeps making the same mistake:

1. Correct it explicitly in the prompt: "Don't do X, do Y instead"
2. Check if Auto Memory picked it up (`/memory`)
3. If not, add it to CLAUDE.md permanently

This escalation path ensures important constraints survive beyond any single session.
