# Commands & Rules

Two primitives that are easy to confuse. They solve adjacent problems, but their lifecycles are different.

## Commands

> A slash-command I want to invoke by name.

A command is a markdown file in `.claude/commands/` that defines a reusable workflow. Typing `/my-command` reads the file and follows the instructions. Commands can take arguments, reference other files, and chain into other commands.

### When it's a command

- **There's a clear trigger.** "Ship this," "run a code review," "create a commit," "plan a phase."
- **It's a sequence, not a capability.** A command says *"do these steps in order"* — skills say *"here's what you can do when asked."*
- **I want it to feel like a keystroke.** Commands are for muscle memory.

### Examples I rely on

| Command | What it does |
|---------|--------------|
| `/commit` | Review diff → draft message → stage → commit following project conventions |
| `/gsd:plan-phase` | Orchestrator for discuss → research → plan → verify |
| `/gsd:execute-phase` | Wave-based execution of a plan with atomic commits |
| `/gsd:ship` | PR creation + review + pre-merge checks |
| `/gsd:debug` | Scientific-method debug session with persistent state |
| `/simplify` | Three parallel review agents suggest simplifications |

### Command vs. skill

The distinction I use: **a command is invoked by the user; a skill is invoked by the agent.** If I want to type it with a slash, it's a command. If the agent should discover it by matching a description, it's a skill.

In practice, a command often *calls* skills internally. `/gsd:plan-phase` invokes a research skill, a planning skill, and a plan-check skill. The command is the choreography; skills are the moves.

## Rules

> Domain knowledge the agent should load only when a topic comes up.

Rules live in `.claude/rules/` and get pulled into context on demand — when the user asks about authentication, load `authorization.md`; when they touch the database, load `supabase-database.md`. Rules prevent the "stuff a 500-line CLAUDE.md into every session" anti-pattern.

### Anatomy

A rule file is just markdown. What makes it a rule (not a note) is that:

1. It's **topical** — one domain per file.
2. It's **loaded on demand** — not injected into every session.
3. It's **referenced from CLAUDE.md** so the agent knows to go find it.

The CLAUDE.md entry might look like:

```markdown
## Domain rules

Before working in a given domain, read the relevant rule file:

- `./.claude/rules/authorization.md` — RLS, admin client usage, auth patterns
- `./.claude/rules/supabase-database.md` — schema conventions, migrations, types
- `./.claude/rules/ui-components.md` — component patterns, design system, a11y
- `./.claude/rules/e2e-testing.md` — Playwright patterns, test layout, fixtures
- `./.claude/rules/deployment.md` — deploy flow, env vars, rollback
- `./.claude/rules/server-layer.md` — server actions, route handlers, data access
```

### When it's a rule vs. CLAUDE.md

CLAUDE.md is always-in-context. It should be short, high-level, and universal to the project — stack, structure, essential commands, git flow. Domain-specific depth goes in rules.

If you're editing CLAUDE.md past 150 lines, stop and ask which sections could become rules.

### When it's a rule vs. a memory

Rules are project-bound knowledge; memories are user-bound preferences. A rule says "in this project, RLS is used for authorization." A memory says "this user prefers terse responses." Different scopes, different files.

## Source

Claude Code commands: code.claude.com/docs/en/commands. Rules / modular CLAUDE.md: the `claude-md-management` plugin in the official marketplace has a solid walkthrough.
