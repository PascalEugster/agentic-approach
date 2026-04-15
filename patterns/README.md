# Patterns

Reusable shapes for agentic work. A pattern is worth naming if I've used it on more than one project and it changes how I'd tackle the next one.

## Index

- [**QRSPI**](qrspi.md) — Question → Research → Structure → Plan → Implement. Dex Horthy's 2026 evolution of RPI. My default for non-trivial features.
- [**Planner / Generator / Evaluator**](planner-generator-evaluator.md) — Anthropic's 3-agent GAN-inspired harness. An independent evaluator breaks the "agents can't judge their own work" problem.
- [**Ralph Loop**](ralph-loop.md) — Geoffrey Huntley's tight `while :; do claude -p < PROMPT.md; done` loop. Less needed now that models one-shot more, but still useful for overnight mechanical work.
- [**Blueprint Pattern**](blueprint-pattern.md) — Stripe Minions-style interleaving of deterministic and agentic nodes. The boring lesson: most of your leverage comes from the deterministic half.
- [**Orchestrator ↔ Worker**](orchestrator-worker.md) — Baseline hierarchy. One main context dispatches isolated sub-agents with filtered tool access.
- [**Agent Teams**](agent-teams.md) — Claude Code's experimental peer-to-peer multi-agent mode. Sub-agents talking to each other, not just to the orchestrator.

## How I pick

> **One task, fresh context window, clear spec** → just prompt it. Don't pattern-match everything.
> **Multi-step feature with design decisions** → QRSPI.
> **Large build where self-evaluation fails** → Planner / Generator / Evaluator with a Playwright MCP evaluator.
> **Overnight refactor, migration, or "grind until green"** → Ralph Loop.
> **Integrating with real infra (CI, deploys, code review)** → Blueprint.
> **Parallel independent features** → Orchestrator + worktree-isolated sub-agents.

The patterns compose. A QRSPI plan phase can spawn workers via Orchestrator ↔ Worker. A Blueprint deterministic node can invoke a Ralph Loop. Nothing here is mutually exclusive.
