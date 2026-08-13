# AGENT SKILLS

You can create agent skills that will be extra, dynamically loaded and give more context. For example: **React Component Best Practices**

- Create a folder called `skills`. **THE NAME `skills` IS MANDATORY**
- Create a subfolder (example: `modern-best-practice-react-components`)
- Then, create a [SKILL.md](/claude/skills/modern-best-practice-react-components/SKILL.md)

You can also add some more instructions that will use your skill as a base. Just add `references` folder and create another markdown, giving more details about something that AI should do/avoid.

The skills also appears as a Claude command `/modern-best-practice-nextjs`. The best approach is that Claude Code will use it and discover it as working on a project, but you can call it directly typing `/...` command. If you give some instructions telling Claude what it should do, or attaching some scripts to run beforehand, it will do so.

# INSTRUCTOR'S NOTE

## ENHANCING SKILLS AND ADDING THIRD-PARTY SKILLS
```md
You learned how you can add custom skills.

If you find that your custom skills are not getting used by Claude Code, try tweaking the `description` of the skill - because, as explained, it's the `title` + `description` that's exposed to Claude Code and that therefore is important for its *"automatic discoverability"*.

You can try adding extra instructions like _"Use this skill to optimize React components and check for the usage of common best practices"_ or _"Gets triggered when writing ANY kind of CSS code"_.

If you DON'T want automatic invocation (i.e., if you want to use skills as *custom commands* only), you can add `disable-model-invocation: true` to the skill metadata (i.e., next to `title` and `description`). If you want the opposite (i.e., ONLY automatic discoverability, NO user invocation) you can add `user-invocable: false`.

You can also restrict which tools Claude Code may use when triggering this skill - via the `allowed-tools` metadata. For example, adding `allowed-tools: Read` allows Claude Code to read files but prevents it from editing or creating. You find a list of available tools [here](https://code.claude.com/docs/en/settings#tools-available-to-claude).

If your skill should work with extra input (e.g., when building it as a custom command), you can use the special `$ARGUMENTS` placeholder in your skill content.

For example, you could build a specialized `code-review` skill like this:

`.claude/skills/code-review/SKILL.md:`

```md
---
name: code-review
description: Review code for bugs, security or performance issues. Use this skill when asked to perform code reviews or after finishing major tasks or after refactoring code.
allowed-tools: Read
description: Perform a code-review
---
 
MODE: $ARGUMENTS
 
If Mode is one of the following, adjust the review as described:
 
- MODE == BUGS: Focus ONLY on logical or other bugs.
- MODE == SECURITY: Focus ONLY on security issues.
- MODE = PERFORMANCE: Focus ONLY performance issues.
 
MODE can also be set to a combination like "BUGS,SECURITY" etc => Perform the combined review in that case.
 
If MODE is set to anything else or nothing at all, perform a thorough, general code review.
 
Perform an in-depth code review of the entire codebase.
 
Carefully and thoroughly explore the codebase file-by-file to find potential issues and improvements.
 
Don't rush it, instead make sure you fully understand the code structure and architecture.
 
Create a detailed report of all your findings.
```

You'll also see a different way of creating custom commands later in the course.

---

**Adding Third-Party Skills**
Building custom skills is a powerful ability, because it allows you to really tailor your Claude Code experience & behavior to your specific (project) requirements and preferences.

But you can also add third-party skills - of course also in addition to your custom skills.

A popular and growing (free!) public skills repository is [skills.sh](https://www.skills.sh/).

There, you find a broad selection of skills you can add to your project. You can install skills from that repository into your project via `npx skills add <owner/repo>` (requires Node.js to be installed on your system, since `npx` is a tool installed together with Node.js).

Of course, you can and should explore those skills you installed. You can tweak them or remove selected parts of the skills.

/hooks

/plugin