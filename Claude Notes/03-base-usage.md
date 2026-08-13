# Base Usage — Claude's Notes

> Course notes: [markdown/base-usage.md](../markdown/base-usage.md)

## The Mental Model: You Are the Architect

The course makes an important point that's easy to undervalue: **you are in control**. Claude Code is a powerful executor, but it needs direction. The quality of the output is a direct function of the quality of your input.

Think of Claude Code as a very fast, knowledgeable contractor. It will build whatever you describe — but if the blueprint is vague, the house will be wrong.

## Commands Worth Memorizing First

From the cheat sheet, the highest-ROI commands to internalize early:

```bash
# Starting
claude                          # New session
claude -c                       # Continue last session
claude "my prompt"              # New session with prompt
claude --agent DocsExplorer     # Start with a specific agent

# Inside a session
/clear          # Fresh context, same directory
/compact        # Summarize + clear (keeps a summary)
/model          # Switch AI model
/init           # Generate CLAUDE.md from codebase scan
/permissions    # Review and update what's allowed
/rewind         # Undo last action (prefer git over this)
```

## Plan Mode: Use It Almost Always

Plan mode (`--permission-mode plan` or `SHIFT+TAB` to toggle) forces Claude to describe what it intends to do before doing anything. This catches mistakes before they happen.

**When to skip Plan Mode**:
- Single-file edits with obvious scope
- Trivial text changes
- Running a test or checking output

**When to always use Plan Mode**:
- Any multi-file change
- Architecture decisions
- Adding new features
- Refactoring existing code
- Anything you haven't done with Claude before

The cost of Plan Mode is a few extra seconds. The benefit is catching wrong assumptions before they cascade into broken code.

## `/rewind` vs Version Control

The course correctly notes that `/rewind` (same as `ESC+ESC`) is unreliable. Always prefer:

```bash
git add -p          # Stage selectively
git commit -m "..."  # Create a checkpoint before big Claude tasks
```

If Claude produces a bad result, `git checkout` is the reliable escape hatch. Treat each significant Claude task like a database transaction — commit before, roll back if needed.

## Reading the Cheat Sheet

The cheat sheet image (`assets/claude-code-cli-settings-cheat-sheet.jpg`) is dense but worth studying. The key insight: there are three different places to look for controls:
1. **CLI flags** — set at session start (`claude --model opus`)
2. **In-session shortcuts** — while working (`SHIFT+TAB`, `CTRL+C`)
3. **Slash commands** — explicit actions (`/model`, `/clear`)

They overlap in capability but differ in when they're useful.

## Injecting Prompts Mid-Task

`ESC` (not `CTRL+C`) lets you interrupt Claude mid-task and inject a new instruction. This is powerful when you see Claude going in the wrong direction — you don't have to wait for it to finish a wrong implementation.

`CTRL+C` cancels and loses the work. `ESC` pauses and lets you redirect.

## Verbose Output

`CTRL+O` toggles verbose mode, which shows the tool calls Claude is making. This is extremely useful for:
- Understanding what files Claude is reading
- Debugging why Claude made a wrong assumption
- Learning what context Claude actually has

Turn it on when results are surprising. Turn it off when you trust the session.

## Background Tasks

`CTRL+B` moves a task to the background. Claude Code is smart about doing this automatically for long-running exploration tasks. This lets you keep the session active and type other prompts while exploration continues. The results come back when the background task completes.
