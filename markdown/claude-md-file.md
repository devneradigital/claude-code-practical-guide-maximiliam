# CRAFTING GREAT CLAUDE.md FILES

Instructor puts a little bit more information at the beginning of the CLAUDE.md file:

```markdown
We're building the app described in @SPEC.md . Read that file for general architectural tasks or to double-check the exact database structure, tech stack or application architecture.

Keep your replies extremely concise and focus on conveying the key information. No unnecessary fluff, no long code snippets.
```

It was also commented that we can have **multiple CLAUDE.md files** just by nesting these files into some folder. Claude code will read these CLAUDE.md files every time it touches some code of that specific folder.

# CLAUDE.md vs "Auto Memory"

Instructor's note:

```md
Recent versions of Claude Code support two different ways of managing general information, instructions & memory:

1. `CLAUDE.MD` files (see previous lectures)

2. Auto Memory

## CLAUDE.MD
`CLAUDE.MD` files are maintained by **YOU**. You put **crucial, general information and instructions** you want to provide to Claude Code for **every (!) session** into this file.

You should keep the file focused and concise to avoid cluttering session context with unnecessary information or instructions (which would consume limited context window space and could lead to worse model performance).

## Auto Memory
Since version 2.1.59, Claude Code has an additional, kind of related, feature called **"Auto Memory"**.

This feature is also about saving information & instructions and loading them into every new session. But "Auto Memory", as the name suggests, is about **Claude Code autonomously storing important information or instructions**. So for this kind of memory, it's not you, it's Claude Code.

Claude Code does store that information in `MEMORY.MD` files in a global `~/.claude/projects/<project>/memory/` path. In addition to this `MEMORY.MD` file, Claude Code may also create topic-specific memory files in that path (per project).

Claude loads parts (the first 200 lines of the `MEMORY.MD` file) of the memory for every new conversation and the entire memory files for every new session. It's configured / "taught" to keep those memory files concise (for the same reason as the `CLAUDE.MD` files mentioned above).

The idea behind this "Auto Memory" feature is simply that you don't have to think about everything you want Claude Code to "remember". Instead it tries to learn from your instructions. Chances are, for example, that it would store information / instructions related to an error it made, which you maybe called out multiple times. Or about a certain code style you corrected it to use.

You can disable / enable and configure this "Auto Memory" feature via the `/memory` command inside of Claude Code. This command also allows you to browse and edit the memory files belonging to your project (if any exist).

"Auto Memory" therefore isn't a replacement for a customized `CLAUDE.MD` file, instead you can think of it as an additional help which could lead to better results. If you know about a certain piece of information or about a specific instruction you want Claude Code to follow in all your sessions, you of course shouldn't hope for it to memorize it => Instead put it into `CLAUDE.MD` yourself!
```