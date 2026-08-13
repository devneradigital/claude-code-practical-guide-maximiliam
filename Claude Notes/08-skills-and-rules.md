# Skills & Rules — Claude's Notes

> Course notes: [markdown/skills-and-rules.md](../markdown/skills-and-rules.md)
> Example skills: [claude/skills/](../claude/skills/)

## What Skills Are

Skills are dynamically-loaded instruction sets that expand Claude's capabilities for specific domains. Unlike CLAUDE.md (which is always loaded), skills are loaded **on demand** — either automatically when Claude detects relevance, or explicitly via slash commands.

**The mental model**: Skills are like reference cards a specialist keeps in their pocket. A React developer doesn't memorize every best practice — they consult a style guide when starting a component. Skills work the same way.

## Directory Structure

```
.claude/
  skills/
    my-skill-name/
      SKILL.md              ← Required: the skill instructions
      references/           ← Optional: supplementary reference files
        some-reference.md
```

The folder name becomes the slash command: `skills/modern-best-practice-react-components/` → `/modern-best-practice-react-components`

## Anatomy of a SKILL.md

```markdown
---
name: modern-best-practice-react-components
description: Build clean, modern React components that apply common best
             practices and avoid pitfalls like unnecessary useEffect usage.
allowed-tools: Read
---

# Your skill instructions here
...
```

Key frontmatter fields:
- **name**: The slug for the slash command
- **description**: Used for auto-discovery — Claude reads this to decide if the skill is relevant
- **allowed-tools**: Restricts which tools Claude can use while this skill is active
- **disable-model-invocation: true**: Prevents auto-invocation — skill only usable via slash command
- **user-invocable: false**: Prevents slash command usage — skill only auto-invoked

## Skills vs CLAUDE.md vs Auto Memory

| | Skills | CLAUDE.md | Auto Memory |
|--|--------|-----------|-------------|
| **Who writes it** | You | You | Claude |
| **When loaded** | On demand or auto-detected | Every session | Every session |
| **Scope** | Narrow (one domain) | Global | Global |
| **Best for** | Domain-specific best practices | Project architecture | Learned preferences |
| **Slash command** | Yes | No | No |

## Auto-Discovery: Making Skills Trigger Automatically

Claude reads the `description` to decide when to load a skill. The trick is writing descriptions that match the actual trigger conditions:

**Weak (often missed)**:
```
description: React component helper
```

**Strong (consistently triggered)**:
```
description: Build clean, modern React components. Gets triggered when writing
ANY React component code. Use this to enforce best practices and avoid common
pitfalls like unnecessary useEffect usage.
```

If a skill isn't being used automatically, rewrite the description — that's usually the fix.

## The $ARGUMENTS Pattern

Skills can accept arguments, enabling parameterized slash commands:

```markdown
---
name: code-review
description: Review code for bugs, security or performance issues.
allowed-tools: Read
---

MODE: $ARGUMENTS

- MODE == BUGS: Focus only on logical bugs
- MODE == SECURITY: Focus only on security issues
- MODE == PERFORMANCE: Focus only on performance issues
- Otherwise: General code review

[review instructions...]
```

Usage:
```
/code-review SECURITY
/code-review BUGS,PERFORMANCE
/code-review   ← general review
```

This is far more powerful than hardcoding different skills for each review type.

## References: Supplementing Skills with Detail

The `references/` folder lets you separate extensive reference material from the main skill instructions. Claude reads the SKILL.md first, then consults references when needed.

Example from the course project:
```
skills/modern-best-practice-react-components/
  SKILL.md              ← "Avoid useEffect — see you-dont-need-useeffect.md"
  references/
    you-dont-need-useeffect.md   ← detailed guidance Claude reads on demand
```

This keeps the main skill file scannable while still providing depth when needed.

## Third-Party Skills: skills.sh

The course mentions [skills.sh](https://www.skills.sh/) — a public repository of community skills. Install via:

```bash
npx skills add <owner/repo>
```

Worth browsing if you work with a framework that has active community skills. Even if you don't use them as-is, they're good reference for how to structure your own.

## The Hooks & Plugins Connection

The course notes for skills reference `/hooks` and `/plugin` at the bottom (as slash commands to invoke). These are upcoming topics that build on the skills system:
- **Hooks**: Automate running skills or prompts at specific lifecycle events (before file save, after commit, etc.)
- **Plugins**: Extend Claude Code with additional tools and capabilities beyond what MCP and skills provide

## Practical Skill Examples in This Repository

The `claude/skills/` directory shows real examples:

| Skill | Purpose |
|-------|---------|
| `modern-best-practice-react-components` | React 19+ best practices, avoid useEffect anti-patterns |
| `modern-best-practice-nextjs` | Next.js app router patterns |
| `clean-typescript` | TypeScript style and type-safety rules |
| `modern-accessible-html-jsx` | Accessibility best practices for JSX |
| `modern-browser-apis` | Use modern browser APIs over legacy patterns |
| `modern-tailwind` | Tailwind CSS v4 patterns |
| `web-security` | Security review checklist for web apps |
| `bun-first` | Bun-specific APIs and idioms |

Each of these is a real, usable skill you can study as a template for your own.
