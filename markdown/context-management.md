# PROMPT and CONTEXT ENGINEERING

Providing the good information is key! Good input -> good results

Combination of Specific instruction(s) + Relevant context / extra information

# Step-by-step

## First thing is: **WHAT IS THE PROMPT?**

Good thing to ask some AI (ChatGPT, Deepseek, Google Gemini...)

Describing:
- General idea of the app: "I'm building a ... / In this app, users will be ...";
- Detailed idea of something important in this app: "In detail, authenticated users can ...";
- Tech stack;
- Better information of libraries and third parties;
- Additional relevant information of the stack;
- Questioning if more description is needed.

## Then, copy the output. Create a new file `SPEC.md` and initiate claude.

Example of input:

```md
We're building an app described in @SPEC.md .

Please format this file as proper markdown.

Also update the file and update the part about {...}

Here's the official {...} documentation article:

<name-of-library-docs>
[pasted text]
</name-of-library-docs>
```

## With the SPEC.md file changed by claude:

You can manually initialize and install dependencies in your project, because sometimes AI doesn't install some dependencies, just change the `package.json` file. (maybe it can be solved by telling AI to use Context7 MCP???)

So, after that, initialize a new session of claude, type `/init` and it will read all files AND the SPEC.md file.

Then, finally, it will create a [`CLAUDE.md`](/markdown/claude-md-file.md) file.

# INSTRUCTOR'S RECOMMENDATION

```md
As explained, writing good prompts and providing helpful context is essential for achieving good results.

Therefore, here are some recommendations / best practices you should consider.

---

**Concise & Precise**

Keep your prompts concise and focused. Don't overdo, you should, of course, accurately and clearly describe the task you want the AI to tackle. But absolutely avoid including details that don't matter or unnecessary fluff.

---

**No Unnecessary Context**

Providing useful (!) context is crucial. But providing context that actually doesn't matter can be counterproductive.

For example, avoid referencing (@some-file) a file you **THINK** may matter. Focus on pointing at files you **KNOW** matter.

The same is, of course, true for documentation articles or any other kind of information that's not really relevant => Avoid it!

---

**Think, Plan, Prompt**

When working with AI, you may be tempted to just start typing and then "fix stuff over time". Don't do that!

Think first, then make a plan, then write a good prompt. If you find yourself asking for follow-ups or providing clarifications a lot, consider putting more effort into upfront planning. Even when using "Plan" mode (which you should use for all tasks that aren't super trivial).

---

**Don't "Test" The AI**

If you know that a task will need the AI to overcome a certain challenge, or if you know about particularly challenging part involved by a task, DON'T hide that information from the AI.

Sure, it can be a nice feeling to see the AI struggle and fail, but if you want to get the job done, share all crucial information with the AI agent.

So, for example, if you know about a particular pitfall or common mistake, include it (AND a recommended solution) in your initial prompt.

---

**Explicitly Tell The AI About Tools It Should Use**

AI agents like Claude Code can use many tools - some built-in (like the "bash" tool, web requests etc.) and some provided via MCP servers (**covered later in the course**). There also are features like "subagents" or "skills" you can leverage (**also covered later**).

If you know that a certain tool or feature should be used for a (part of a) task - explicitly tell the AI to do so. Don't hope it automatically uses the right tool or feature just because it theoretically could.

---

As you can tell, there's one main theme across pretty much all recommendations: YOU are in control. YOU steer the AI.
```

# LEVERAGING PLAN MODE

First, `/clear` the session.

Even if it's simple, you should **ALWAYS** use Plan Mode at first.

Plan Mode will tell Claude that it needs to sum up everything before doing any changes or implementation in your code. Also, it will read all files available in your folder, and also `CLAUDE.md`.