# Configuration — Claude's Notes

> Course notes: [markdown/configuration.md](../markdown/configuration.md)

## The Three-Layer Settings Hierarchy

Understanding which settings file wins is critical:

```
~/.claude/settings.json          ← Global (all projects, all users)
    ↓ overridden by
.claude/settings.json            ← Project (committed, shared with team)
    ↓ overridden by
.claude/settings.local.json      ← Local (not committed, personal overrides)
```

**Practical rule**: put team-shared constraints in `.claude/settings.json` (e.g., "never touch `.env` files"), and personal preferences in `settings.local.json` (e.g., your preferred model or verbosity).

## Permissions: The Most Important Configuration

The permission system determines what Claude Code can do autonomously vs. what requires your approval. This is where you control the blast radius.

### Permission Modes (SHIFT+TAB cycles through them)

| Mode | What It Allows |
|------|---------------|
| Default | Read files freely, ask before writing or running commands |
| Write Permissions | Edit files without asking |
| Plan | Only plan — no code changes at all |

**Never use `--dangerously-skip-permissions` outside a sandbox.** If you need that level of autonomy, use Docker:

```bash
docker sandbox run claude
# or the native sandbox:
# /sandbox inside a session
```

### Fine-Grained Permissions in settings.json

```json
{
  "permissions": {
    "allow": ["Read", "Write(src/**)"],
    "deny": ["Read(.env)", "Bash(rm -rf)"]
  }
}
```

This is how you let Claude freely edit your source code but prevent it from reading secrets or running destructive commands.

## Model Selection

The `/model` command (or `Alt+P`) switches models mid-session. Key consideration:

- **Opus**: Most capable, use for architecture decisions, complex refactors, novel problems
- **Sonnet**: Balanced speed/quality, good for most daily tasks
- **Haiku**: Fastest and cheapest, use for simple edits or when running in CI

You can set the default in `settings.json`:
```json
{ "model": "opus" }
```

But you'll often want to start a session with a cheaper model and switch to Opus only when you hit a genuinely hard problem.

## Compaction: What You Lose and How to Prevent It

When the context window fills up, Claude Code auto-compacts: it summarizes the conversation history into a shorter representation. The risks:

- **Lost nuance**: Exact wording of previous instructions may change
- **Lost decisions**: Why you chose approach X over Y might disappear
- **Lost error history**: A bug you told Claude to avoid may be "forgotten"

**Defensive strategies**:
1. Use `/compact` proactively before starting a new major task within the same session
2. Better yet, start a **new session** (`/clear`) for each distinct task
3. Put recurring constraints in `CLAUDE.md` so they survive session boundaries

## Environment Variables in Settings

```json
{
  "env": {
    "NODE_ENV": "development",
    "DATABASE_URL": "postgresql://localhost/myapp"
  }
}
```

This injects env vars into every session. Useful for dev-only vars, but **never put secrets here** — `settings.json` is committed to git. Secrets belong in `.env` (not committed) and should be in the `deny` list for Claude's file access.

## alwaysThinkingEnabled

This is on by default and enables Claude's extended reasoning. The course notes say turning it off yields worse results — this is accurate. The "thinking" is where Claude plans multi-step problems before acting. Disable it only if latency is critical and tasks are trivial.
