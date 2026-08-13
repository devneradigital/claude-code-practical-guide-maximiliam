# Hooks — Claude's Notes

> Course notes: [markdown/hooks.md](../markdown/hooks.md) *(not yet written — notes pending)*

## What Hooks Are

Hooks are shell commands that Claude Code executes automatically at specific lifecycle events — without you having to ask. They're configured in `settings.json` and run by the Claude Code harness, not by the AI itself.

This distinction matters: hooks are **deterministic automation**, not AI behavior. They always run exactly as configured, regardless of what Claude is doing.

## When Hooks Fire

Claude Code supports hooks at the following lifecycle points:

| Hook Event | When It Runs |
|-----------|-------------|
| `PreToolUse` | Before Claude calls a tool (e.g., before writing a file) |
| `PostToolUse` | After Claude calls a tool (e.g., after writing a file) |
| `Notification` | When Claude sends a notification |
| `Stop` | When Claude finishes a task (session ends or task completes) |

## Configuration in settings.json

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "npm run typecheck"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Claude finished. Review changes in git status.'"
          }
        ]
      }
    ]
  }
}
```

The `matcher` filters which tool events trigger the hook. Omitting it means "all events of this type."

## Practical Use Cases

### Automatic Type Checking

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write",
      "hooks": [{ "type": "command", "command": "npx tsc --noEmit" }]
    }]
  }
}
```

Every time Claude writes a file, TypeScript validates it automatically. Claude sees the output and can self-correct.

### Linting and Formatting

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write",
      "hooks": [{ "type": "command", "command": "npx eslint --fix $CLAUDE_TOOL_INPUT_FILE_PATH" }]
    }]
  }
}
```

Auto-format every file Claude writes, so output is always clean before you see it.

### Test Runner on File Change

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write(src/**/*.test.ts)",
      "hooks": [{ "type": "command", "command": "bun test $CLAUDE_TOOL_INPUT_FILE_PATH" }]
    }]
  }
}
```

Run tests automatically when Claude writes test files.

### Completion Notification

```json
{
  "hooks": {
    "Stop": [{
      "hooks": [{
        "type": "command",
        "command": "notify-send 'Claude Code' 'Task complete'"
      }]
    }]
  }
}
```

Desktop notification when a long-running task finishes.

## Hook vs Claude Instruction

**Hook**: "After every file write, run the linter" → reliable, always runs, no AI judgment needed  
**CLAUDE.md instruction**: "Always run the linter after writing code" → AI interprets this, may or may not do it, depends on context

Use hooks for **guarantees**. Use instructions for **guidance**. Don't use instructions for things that must always happen.

## Environment Variables Available in Hooks

Claude Code exposes context to hooks via environment variables:
- `$CLAUDE_TOOL_NAME` — name of the tool that was called
- `$CLAUDE_TOOL_INPUT_FILE_PATH` — file path (for file tools)
- `$CLAUDE_SESSION_ID` — current session identifier

## Hook Output Goes Back to Claude

If a hook command produces stdout, Claude sees it and can react. This creates a feedback loop:

1. Claude writes a file
2. `PostToolUse` hook runs `tsc --noEmit`
3. TypeScript finds an error → outputs error message
4. Claude reads the error and corrects the file

This is more reliable than asking Claude to "remember to check types" — the hook enforces the check, and Claude auto-corrects from the feedback.

## Scope: Global vs Project

Hooks in `~/.claude/settings.json` apply globally. Hooks in `.claude/settings.json` apply only to the current project. Keep project-specific hooks in the project settings so they travel with the repository.
