# Agentic Approach

> A personal field-guide to how I build software with AI agents.
> Less a framework, more a running log of what sticks, what breaks, and what I'm still experimenting with.

> **Honest disclaimer.** This is a personal notebook, not a verified reference. Tool names, feature lists, and links to other people's work are as I understood them at the time of writing. Specific numbers, version details, quotes, and URLs rot fast — when something here looks important, check the official source before relying on it. If you spot an error, treat this repo as a starting point for your own investigation, not as the last word. Full disclaimer in [`DISCLAIMER.md`](DISCLAIMER.md).

---

## Start here — if you're new to agentic engineering

If you came from a talk and this is your first real look at the space, don't try to read everything. Read these, in order:

1. **This README** — the mental model, then the [*What my harness actually contains*](#what-my-harness-actually-contains) section to see what a real setup looks like in concrete terms.
2. [**`tools/claude-code.md`**](tools/claude-code.md) — what Claude Code actually is and what primitives it exposes.
3. [**`primitives/README.md`**](primitives/README.md) — skills, hooks, sub-agents, commands, rules, MCP. The building blocks you'll use to assemble your own harness.
4. [**`patterns/README.md`**](patterns/README.md) — the named shapes for agent work. Skim the index, then read the one that sounds most relevant to what you'd build first.
5. [**`case-studies/stripe-minions.md`**](case-studies/stripe-minions.md) — concrete example of how all of this fits together at production scale.

Everything else is reference material — dip in when you need it, not front-to-back.

**Prerequisites.** This repo assumes you know (or will look up) what Claude Code is and how to install it. The short answer: it's a CLI tool from Anthropic, installed from their official site; check the current docs at `code.claude.com` for the install command of the moment. Everything in this repo is "sugar on top" of having it working.

**Not using Claude Code?** Most of the patterns here are harness-agnostic. They apply to Cursor, Codex, Devin, or anything else with skills, hooks, and sub-agent primitives — the names are different, the shapes are the same. See [`tools/landscape-2026.md`](tools/landscape-2026.md) for the alternatives.

---

## Why this repo exists

Most writing about "AI coding" stops at *prompt the model, paste the result*. That stopped being the interesting part a while ago. The interesting part is the **harness**: the layer of skills, hooks, sub-agents, rules, and workflows that turn a stateless LLM into a collaborator that can actually ship production code without me hovering over it.

This repo is my working document for that. It explains:

- the mental model I use when I sit down to build something with an agent,
- the concrete primitives I wire together (Claude Code skills, rules, hooks, sub-agents, custom commands, plugins, and the MCP servers that connect them),
- the patterns I've learned from running this setup across several real client and product projects,
- and what I'm still figuring out.

It is deliberately personal. If you're reading it and something doesn't match how you work — good. Copy what's useful, ignore the rest.

---

## Repo map

The README is the opinionated overview. The folders below are the reference material — each has its own `README.md` as an index.

- [`patterns/`](patterns/) — QRSPI, Planner/Generator/Evaluator, Ralph Loop, Blueprint, Orchestrator ↔ Worker, Agent Teams.
- [`primitives/`](primitives/) — Skills, hooks, sub-agents, commands & rules, MCP.
- [`context-engineering/`](context-engineering/) — The five strategies and Breunig's anti-pattern taxonomy.
- [`workflows/`](workflows/) — phase workflow, worktrees, and a decision table for workflow shape vs. task size.
- [`tools/`](tools/) — Claude Code, the 2026 competitive landscape, agent frameworks.
- [`benchmarks/`](benchmarks/) — SWE-bench / Terminal-Bench, the METR productivity paradox, quality & security cost.
- [`case-studies/`](case-studies/) — Spotify Honk, Stripe Minions, and shorter writeups on Shopify, Anthropic, TELUS, Zapier, Rakuten.
- [`resources/`](resources/) — Reading list, people to follow, glossary.
- [`essays/`](essays/), [`playbooks/`](playbooks/), [`templates/`](templates/) — Placeholders for longer-form notes, runbooks, and starter artifacts.

---

## The mental model: harness, not magic

I think of every serious AI-assisted project as three layers:

1. **The model** — the raw reasoning engine (Claude, GPT, Gemini). I treat this as a commodity. Models change every few months; my setup shouldn't collapse when they do.
2. **The [harness](resources/glossary.md#agent-harness--harness)** — the orchestration layer around the model. Context management, tool dispatching, safety checks, sub-agent spawning, state persistence, [hooks](resources/glossary.md#hook) firing at lifecycle events. This is where most of my leverage lives. Claude Code is the harness I use day to day — but "Claude Code" on its own is a blank slate. What makes *my* harness useful is the custom layer I've built on top of it: skills, rules, a reviewer sub-agent, custom commands, the hook baseline, and the way artifacts land on disk. See [*What my harness actually contains*](#what-my-harness-actually-contains) below for the concrete pieces.
3. **The workflow** — the repeatable processes I run on top of the harness. For me this is a phase-based discuss → research → plan → execute → verify loop, with each phase living in its own sub-agent and producing an artifact on disk. I use a plugin called [GSD](resources/glossary.md#gsd) for the phase orchestration, but it's one ingredient, not the identity of my setup — and most of the actual leverage comes from the custom layer, not from the framework.

Most of my time improving "how I work with AI" is actually spent on layers 2 and 3 — not on picking a better model or writing cleverer prompts.

> Unfamiliar with a term? Jump to [`resources/glossary.md`](resources/glossary.md). Terms like *harness*, *sub-agent*, *MCP*, *QRSPI*, *Blueprint*, *Ralph Loop*, and *Planner / Generator / Evaluator* are all defined there with links back to the detailed pages.

---

## The primitives I actually use

Claude Code exposes a small set of composable primitives. The trick is knowing which one to reach for. My rough decision tree:

| Primitive        | When I reach for it                                                                 | Lives in                         |
|------------------|-------------------------------------------------------------------------------------|----------------------------------|
| **CLAUDE.md / AGENTS.md** | Durable project context — stack, conventions, essential commands, git flow. Loaded into every session. | Repo root + per-package          |
| **Rules**        | Tight domain knowledge the agent should load only when a topic comes up (auth, DB, UI components, testing). | `.claude/rules/`                 |
| **Skills**       | A reusable capability I'll invoke more than twice: `deploy`, `db-migrate`, `playwright-cli`, `ci-status`, feature-specific builders. | `.claude/skills/` and user-level |
| **Commands**     | A specific multi-step workflow I want to invoke with a slash (`/gsd:plan-phase`, `/commit`, `/ship`). | `.claude/commands/`              |
| **Sub-agents**   | Parallel or isolated work — code review, research, UI audit, test generation, debugging — where I want a separate context window and a focused tool set. | `.claude/agents/`                |
| **Hooks**        | Enforcement. Things that must happen every time regardless of what the model "decides": pre-bash safety checks, post-edit lint/format, pre-deploy typecheck, worktree setup. | `.claude/settings.json` + shell scripts |
| **Plugins**      | Vendor- or tool-specific knowledge bundles (Vercel, Supabase, Figma, frontend-design, PR review toolkit). Installed once, available everywhere. | `~/.claude/plugins/`             |
| **MCP servers**  | First-class access to external systems (Linear, Notion, Figma, Next.js devtools, browser automation) without hand-rolling API wrappers. | `.mcp.json` + user settings      |

### Rule of thumb

> *If I've explained it twice, it's a skill. If it must happen every time, it's a hook. If it needs its own context window, it's a sub-agent. If it's project-shaped, it's a phase with artifacts on disk.*

---

## How the layers work together

A realistic example of a feature I'd build on a SaaS project (Next.js App Router + Supabase + Vercel, which is my default stack):

1. **Discuss phase** — `/gsd:discuss-phase` runs an adaptive questioning sub-agent that surfaces assumptions before I write a line of code. Outputs go into `.planning/phases/NN-feature-name/DISCUSS.md`.
2. **Research phase** — a researcher sub-agent pulls in external docs (Context7, Firecrawl, Exa) and writes `RESEARCH.md`. This stays out of my main context window until the planner needs it.
3. **Plan phase** — `/gsd:plan-phase` creates `PLAN.md` with a task breakdown, dependency graph, and goal-backward verification. A plan-checker sub-agent reviews it before I approve.
4. **Execute phase** — the executor works plan tasks one at a time. Every edit triggers a **post-edit lint hook** (`post-edit-lint.sh`). Every Bash call triggers a **pre-bash safety hook** and a **pre-bash typecheck hook** with a 3-minute budget. These are non-negotiable guardrails that don't depend on the model remembering to run them.
5. **Verify phase** — a verifier sub-agent does goal-backward analysis: *does the code actually deliver what the phase promised, not just what the tasks said?* Writes `VERIFICATION.md`.
6. **UI review** — if it's a frontend phase, a UI auditor sub-agent runs a 6-pillar visual review (spacing, hierarchy, motion, accessibility, etc.), often with headless browser screenshots.
7. **Ship** — `/gsd:ship` opens the PR, runs review, and prepares the merge. A separate code-quality-reviewer agent reads the diff independently.

The key move here isn't any single step — it's that **every step runs in an isolated sub-agent context**, so my main session stays cheap and coherent even for a multi-day feature. The orchestrator-worker pattern is what makes this tractable at all.

---

## The harness patterns that actually matter

After running this setup across several projects, these are the patterns I keep coming back to:

### 1. Context is a finite resource — treat it that way

The single biggest performance lever is keeping the main context window relevant and small. That means:

- **Rules over mega-CLAUDE.md.** Split domain knowledge into topic files that load on demand. A 200-line CLAUDE.md that runs on every message burns tokens on rules that aren't relevant to the current task.
- **Sub-agents for anything wide-reading.** Grepping the whole repo, reading long files, or doing multi-file research all go into a sub-agent so the raw output never lands in my main context.
- **Artifacts on disk, not in chat.** Research, plans, and verification reports live in `.planning/`. I can re-read what I need. The model doesn't need to carry them forward indefinitely.

### 2. Hooks are how you enforce invariants

The model will occasionally "forget" to run typecheck, or try to push without linting, or attempt a destructive Bash command. Hooks are the harness saying *no, actually*. My baseline three:

- **PreToolUse / Bash — safety** — block destructive commands (`rm -rf`, force push to main, dropping tables) unless explicitly confirmed.
- **PreToolUse / Bash — typecheck** — before any "run the app" or "ship" command, make sure the code even compiles.
- **PostToolUse / Edit|Write — lint + format** — every file that gets written goes through the formatter immediately. Tiny feedback loop.

Hooks are shell scripts. They run regardless of which model is driving. They cost nothing to add and save real incidents.

### 3. Isolation where it counts

Parallel features go in **git worktrees**, not stashes. A `WorktreeCreate` hook copies `.env` files, installs dependencies, and configures git hooks automatically. Sub-agents can be configured to run in isolated worktrees too, which is how I run "try this refactor in a worktree and throw it away if it doesn't work" experiments without polluting main.

### 4. Externalize memory

Claude Code has a file-based memory system at `~/.claude/projects/<project>/memory/`. I use it sparingly but deliberately — feedback ("never mock the DB in integration tests"), project state (who's doing what, why), and references (where external docs live). Not for things derivable from `git log` or code.

### 5. Pick the right workflow shape for the task

Not everything needs a full phase workflow. My rough mapping:

- **Trivial one-liner** — plain chat, no ceremony. Typos, config tweaks, copy changes.
- **Small focused task** — plain Claude Code session, hooks and skills doing their job, one atomic commit at the end. No planning.
- **Real feature work** — phase workflow: discuss → research → plan → execute → verify, each phase in its own sub-agent, each output on disk. In my setup this runs through the GSD plugin's `/gsd:*` commands, but the *shape* is what matters, not the framework.
- **Debugging a production incident** — a scientific-method debug loop with state that survives context resets. Different loop entirely; my plugin has a command for this, but any tool that persists debug state between sessions works.

Using a too-heavy workflow on a tiny task is just as bad as using a too-light one on a real feature.

---

## What my harness actually contains

"The harness" sounds like one thing. It isn't. Here's what's concretely in mine, across project- and user-level Claude Code configuration. I put this here because the short version — *"I use Claude Code and a plugin called GSD"* — is true but deeply misleading. The plugin gives me a phase shape. **Almost all of the day-to-day leverage is in the custom layer underneath it.**

### Custom skills

Invoked by the agent when the situation matches. Short markdown files, sometimes with helper scripts:

- A **deploy** skill that knows my deploy ritual end-to-end — env check, migration push, build, verify, post-deploy sanity check.
- A **db-migrate** skill for authoring, linking, pushing, and verifying database migrations without me having to remember the exact CLI incantations.
- A **dev-logs** skill that tails the right logs in the right format for whatever stack I'm in.
- A **ci-status** skill that reads CI state, interprets failures, and suggests fixes.
- A pair of **Playwright** skills — one to drive the CLI, one for authoring end-to-end tests against the patterns my project uses.
- A **postgres-expert** skill for review and query tuning.
- A handful of scaffolding skills for the parts of the stack I repeat most — server actions, forms, services. These save me from explaining the project shape every time I want to add a CRUD surface.

The point of skills isn't cleverness — it's *eliminating the "explain the stack again" tax* that shows up every time you start a new chat.

### Topic rules

Loaded on demand when the conversation touches them. Short markdown files in `.claude/rules/`, referenced from `CLAUDE.md` so the agent knows where to look:

- **authorization** — how auth, roles, and row-level security actually work in the stack I'm in.
- **database** — schema conventions, migration style, generated types.
- **ui-components** — component patterns, design system conventions, accessibility defaults.
- **e2e-testing** — Playwright patterns, test layout, fixture conventions.
- **deployment** — the full deploy flow, environment variables, rollback steps.
- **server-layer** — server actions, route handlers, data access patterns.

Rules are *not* loaded on every message. That's the whole point. A 500-line `CLAUDE.md` burns tokens on every turn whether or not the topic is relevant. Topic rules only pay the cost when the work actually touches the topic.

### Custom sub-agents

- A **code-quality-reviewer** sub-agent I invoke at the end of every feature, before the PR. It reads the diff in a fresh context with a dedicated system prompt tuned to catch the things I skim past on fast reviews — unused exports, sloppy error handling, suspicious type casts, inconsistent naming, missing test coverage on edge cases.
- The **specialist sub-agents that come with the phase plugin** — researcher, planner, plan-checker, executor, verifier, UI auditor. Each runs in a fresh context so my main session stays clean.

### Custom commands

- A **feature-builder** command that chains the discuss → research → plan → execute → verify sequence the way I want it, wired to my reviewer agent at the end. Think of it as my personal `/ship` for non-trivial work.
- A few small utility commands on top of Claude Code's base set (`/commit`, `/review`, `/simplify`).

### Hooks — the non-negotiable layer

Three hooks I wouldn't ship a project without, plus one for worktree setup:

- **`PreToolUse / Bash` — safety** — blocks `rm -rf` patterns, force-push to protected branches, direct DB drops, and similar destructive commands.
- **`PreToolUse / Bash` — typecheck** — before any "run the app" or "ship" command, runs the typechecker with a tight budget. Catches roughly half the "I thought I was done" failures I used to see.
- **`PostToolUse / Edit|Write` — lint + format** — every file the agent writes goes through the formatter immediately. Keeps diffs clean, keeps the commit-by-commit history readable.
- **`WorktreeCreate` — setup** — when I spin up a new worktree, copies `.env` files, installs dependencies, and wires the git hooks so the new checkout is immediately runnable.

The hooks are where I stop trusting the model to remember things and start letting the harness enforce them. They cost nothing to add and they prevent real incidents.

### Phase workflow plugin

This is where GSD fits. It gives me a command namespace (`/gsd:*`) for the larger discuss → research → plan → execute → verify loop, plus the specialist sub-agents that run inside each phase. I use it for anything non-trivial.

For small tasks, I go straight to a plain Claude Code session — the skills, rules, and hooks are what I actually rely on, and the phase plugin would be ceremony. **GSD is an amplifier for feature work, not a mandatory entry point.** If you took my GSD usage away and left everything else, I'd still be productive. If you took the custom layer away and left GSD, I'd be running generic phases against a codebase the agent doesn't understand.

### Planning artifacts on disk

Every phase of non-trivial work leaves a trail in a `.planning/` directory:

```
.planning/
├── PROJECT.md         # Project context — stack, constraints, conventions
├── ROADMAP.md         # Phase list with status
├── STATE.md           # Current state machine for the in-flight phase
├── phases/
│   ├── 01-feature-name/
│   │   ├── DISCUSS.md
│   │   ├── RESEARCH.md
│   │   ├── PLAN.md
│   │   └── VERIFICATION.md
│   └── ...
├── codebase/          # Map-of-the-codebase output
└── research/          # Cross-phase research notes
```

Artifacts are how phases communicate. My main session doesn't carry research forward in its context window — the research lives in `RESEARCH.md`, and the planner sub-agent reads it when it needs to. Same for plans and verification reports. **The chat stays cheap; the durable knowledge stays on disk.** A week later I can come back and everything I need is still there.

### MCP servers wired in

Linear for issues, Notion for docs, Figma for design handoff, a browser-automation server for agentic QA, a docs server for up-to-date library references, and a handful of vendor-specific MCPs for the infrastructure I deploy to. The agent can read and write all of these directly instead of me copy-pasting between windows.

### The default stack this is all tuned for

Next.js App Router, React, TypeScript, a Postgres-backed backend, Tailwind + shadcn/ui, Turborepo for larger monorepos, Playwright for E2E. Server Components by default; client components only where interactivity is genuinely needed. Most of my skills and rules assume this stack because it's the one I ship most. The *shapes* are transferable to other stacks; the specifics aren't.

### The message

> **Don't start with the framework. Start with the context, the rules, the hooks, and the reviewer agent. The framework is the last thing to add, not the first.**

A small team with good rules, good skills, and a reviewer sub-agent beats a large team running generic phases over an unstructured codebase. The framework is the visible part; the custom layer is where the work actually lives.

---

## Goals

What I actually want out of this approach:

1. **Ship faster without shipping worse.** The point is higher output *and* higher quality. If one is winning at the cost of the other, the setup is broken.
2. **Stay in the loop without being in the loop.** I want to be able to describe a phase, approve a plan, come back an hour later, and review real work — not watch a progress bar.
3. **Keep the model boring.** The harness should be opinionated enough that the model's job is mostly "do the obvious thing given the rules and the plan." Creativity belongs in architecture decisions, not in whether to run the linter.
4. **Reusable across projects.** Every good pattern I find on one project should port to the next without rewriting it. That's why user-level skills and plugins matter more to me than project-level ones over time.
5. **Teach it.** I can't fully trust an approach until I can explain it to someone else. This repo is part of that.

---

## What I'm still figuring out

A non-exhaustive list of things that are open in my head:

- **Evaluation.** I don't have a real regression harness for my own agent setup. When I change a hook or a rule, I'm mostly eyeballing whether things got better. I'd like something closer to a proper eval suite — golden tasks, scored runs, diffable results. Tooling like Braintrust, Langfuse, and LangSmith exists for exactly this and I haven't adopted it yet.
- **Durable execution for long-running agent work.** My current runs are mostly ephemeral — they live inside a Claude Code session and disappear when I close the terminal. For anything that should survive a crash, retry on failure, or run for hours against external events, the right shape is probably a durable-execution engine underneath (Temporal, Inngest, Vercel's Workflow DevKit, or similar). I'm still figuring out where the boundary is between "in-session is good enough" and "this needs to be a durable workflow."
- **Long-running autonomous loops.** Running phase after phase unattended works (my workflow plugin has a command for it), but I still don't quite trust it on anything critical. The question is what guardrails make it trustworthy — canary checks, auto-rollback, stricter verification gates, an independent evaluator?
- **Multi-agent coordination beyond orchestrator-worker.** Right now everything is strictly hierarchical: one orchestrator, several workers, results bubble up. I'm curious about peer-to-peer agent patterns (supervisor + router + specialists, à la LangGraph 2.0) and whether they'd buy me anything over the current shape.
- **Cost/quality tuning.** Which steps actually benefit from Opus and which could be Haiku without losing quality? I keep meaning to instrument this properly instead of guessing.
- **Better secrets hygiene for agents.** MCP tokens, vendor API keys, and database credentials all have different threat models. A unified story for "which agent can reach which system, with what scope, for how long" would save me real worry.
- **Design-to-code fidelity.** Figma → working component is still the step where I most often have to intervene. Frontend-design plugin + Figma MCP is closer than anything before, but there's headroom.
- **Retrospectives on the agents themselves.** Weekly "what did the agent do well / badly" review, surfaced from logs, not memory. Halfway between `gstack-retro` and a proper eval.

---

## What might get added here over time

This repo is intentionally sparse right now. Likely future additions:

- **`examples/`** — sanitized walk-throughs of a real phase from discuss → ship, with the actual artifacts (DISCUSS.md, PLAN.md, VERIFICATION.md) so the shape is visible.
- **`templates/`** — starter `.claude/` directory for a new project, with the hooks, rules, and settings I'd want on day one.
- **`essays/`** — longer-form notes on specific patterns (why hooks beat prompting, why worktrees beat stashing, why context engineering matters more than prompt engineering).
- **`playbooks/`** — concrete runbooks: "set up a new Next.js + Postgres project with the full harness in under 30 minutes," "debug a production incident with a persistent debug session."
- **A proper eval harness** — once I figure out what mine should look like.

If something feels load-bearing but isn't here yet, it's probably on this list. Poke me.

---

## Further reading

See [`resources/reading-list.md`](resources/reading-list.md) for the full list, grouped by how often I actually come back to it. The short version of my top five:

- [Best Practices for Claude Code — official docs](https://code.claude.com/docs/en/best-practices)
- Simon Willison, *Agentic Engineering Patterns* — continuously updated
- Anthropic, *Harness Design for Long-Running Application Development* (Planner/Generator/Evaluator)
- Martin Fowler / Birgitta Böckeler, *Harness Engineering for Coding Agent Users*
- Dex Horthy, *Context Engineering for Agents* and the QRSPI talk

---

*This is a living document. If you're reading it six months from now and something here looks stale, assume it is, and check `git log`.*
