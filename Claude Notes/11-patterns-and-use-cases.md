# Patterns & Use Cases — Claude's Notes

> Course notes: [markdown/patterns-and-use-cases.md](../markdown/patterns-and-use-cases.md) *(not yet written — notes pending)*

## High-Leverage Patterns

These patterns emerge from combining everything the course covers — they're where Claude Code becomes genuinely transformative rather than just a faster way to type code.

## Pattern 1: The SPEC-First Greenfield Project

**When to use**: Starting a new project from scratch.

```
1. Chat with another AI (ChatGPT, Gemini) to draft a detailed SPEC.md
   → Tech stack, auth model, data schema, key user flows, edge cases

2. Create SPEC.md in your project root
3. Initialize the project structure manually (package.json, tsconfig, etc.)
4. Install dependencies yourself: bun install
5. Start Claude Code: /init → generates CLAUDE.md referencing SPEC.md
6. First task: "Read @SPEC.md. Implement the database schema as described."
```

**Why it works**: Claude starts with a complete mental model of the project instead of inferring it from partial information. Every subsequent task benefits from this shared understanding.

## Pattern 2: The Focused Task Session

**When to use**: Adding a feature or fixing a bug in an existing codebase.

```
1. git commit (create a checkpoint)
2. claude → /clear (or new terminal)
3. Plan mode: "I need to [task]. Read [relevant files]. Plan the changes."
4. Review the plan — push back on anything wrong
5. Exit plan mode → implement
6. Review diff → git commit if good, git checkout if not
```

**Why it works**: Each session has clean context. The git checkpoint means you can always roll back. Plan mode catches wrong assumptions early.

## Pattern 3: The Documentation-First Library Integration

**When to use**: Integrating a library you haven't used recently or that has changed.

```
1. In CLAUDE.md (or the prompt): "Use DocsExplorer to fetch current [library] docs before implementing"
2. Claude spawns DocsExplorer → fetches current API
3. Implementation uses current syntax, not potentially stale training data
```

**Why it works**: Libraries change. Using current docs prevents the common failure of Claude generating code for an old API.

## Pattern 4: The Iterative Refinement Loop

**When to use**: Getting a result close to what you want but needing to tune it.

```
1. Get Claude's first attempt
2. Don't say "that's wrong, try again" — be specific:
   "The button should be full-width on mobile. The text color should use
   the design token --color-primary, not hardcoded blue."
3. Claude updates specifically what you called out
4. Repeat until done
```

**Why it works**: Vague corrections lead to random changes. Specific corrections converge on the target.

## Pattern 5: The Code Review Session

Use a `code-review` skill with `/code-review SECURITY` or `/code-review BUGS` to systematically audit finished work. Run this:
- After a major feature lands
- Before merging to main
- After a refactor

Claude in read-only mode (allowed-tools: Read) gives honest feedback without the risk of it changing things during review.

## Pattern 6: The Debugging Session

When stuck on a bug:

```
1. New session (/clear or fresh terminal)
2. "I have a bug in [component/function]. Here is the error: [exact error]
   Here are the relevant files: @src/auth/login.ts @src/types.ts
   The bug only happens when [specific conditions]."
3. Use Plan mode: let Claude diagnose before touching anything
4. Review the diagnosis — if wrong, correct it with more specific context
```

**Why fresh session**: If you've been fighting a bug in the same session, Claude may have "learned" incorrect assumptions. Fresh context often leads to faster diagnosis.

## Pattern 7: Multi-Agent Parallelism

For complex tasks with independent workstreams:

```
"Simultaneously:
 1. Use DocsExplorer to fetch Bun SQLite docs
 2. Use Explore to map the existing database module structure
 3. Read the SPEC.md database section

 Then, with all that context, implement the user table migration."
```

Claude can run these subagents in parallel, then synthesize the results before writing any code.

## Anti-Patterns to Avoid

### The Long Sprawling Session

**Problem**: Doing 6 different features in one session, letting context get huge.  
**Fix**: One session per feature. Use `/clear` between tasks.

### The Vague Correction

**Problem**: "That's not quite right, try again."  
**Fix**: Always specify what's wrong and what you want instead.

### Trusting Without Verifying

**Problem**: Accepting Claude's output without reading it.  
**Fix**: Always read the diff. Claude is fast and usually right, but not always.

### Testing the AI

**Problem**: Not telling Claude about known pitfalls to see if it figures them out.  
**Fix**: Tell Claude everything relevant upfront. You're not evaluating it — you're collaborating.

### The Monolithic Prompt

**Problem**: "Build me a complete SaaS app with auth, billing, and a dashboard."  
**Fix**: Break it down. Start with schema → then auth → then one feature at a time. Each step can validate against the SPEC.

## Use Case: CI/CD Integration

Claude Code's `-p` (print/one-shot) mode enables non-interactive use in pipelines:

```bash
# In a GitHub Actions workflow:
claude -p "Review the changes in this PR for security issues. Focus on:
  - SQL injection vectors
  - Authentication bypass risks
  - Exposed secrets in code
  Output a JSON array of findings."
```

Combined with hooks and specific permission configurations, this makes Claude Code a programmable review step in your pipeline.

## Use Case: Documentation Generation

```bash
claude -p "Read all TypeScript files in src/api/. Generate API documentation
in markdown format. For each endpoint, document: method, path, request body
schema, response schema, and error conditions. Reference @src/types.ts for
type definitions."
```

Run this as a pre-commit hook or CI step to keep docs in sync with code.

## Use Case: Test Generation

```
"Read @src/auth/login.ts and @src/auth/login.test.ts.
The existing tests cover the happy path. Add tests for:
- Expired JWT tokens
- Malformed tokens
- Missing Authorization header
- Tokens signed with wrong secret
Use the same testing patterns already in the file."
```

Giving Claude both the implementation and existing tests teaches it the testing style before it writes new tests.
