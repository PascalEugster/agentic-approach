# Glossary

Terms I use throughout this repo, in the sense I mean them. The field's vocabulary is drifting fast — these definitions reflect usage in early 2026.

## Agent
A model wrapped in a tool-use loop with some notion of autonomy. "Agent" is a spectrum word; there's no hard line between "chat with tools" and "autonomous agent." When I say "agent" without qualification, I usually mean "Claude Code in a normal coding session."

## Agent harness / Harness
The runtime orchestration layer around the model. Handles tool dispatch, context management, safety checks, sub-agent spawning, state persistence, hooks, session lifecycle. Coined by Mitchell Hashimoto. The layer where most of my leverage lives.

## Agent Teams
A pattern — and, in Claude Code, an experimental feature — where sub-agents can message each other peer-to-peer instead of communicating only through the orchestrator. Feature names and flags change between releases; see the current Claude Code docs for the version you're running.

## Blueprint pattern
Stripe's orchestration model: alternating deterministic and agentic nodes. Deterministic nodes handle context prefetch, tooling, gating. Agentic nodes handle reasoning and generation. See [patterns/blueprint-pattern.md](../patterns/blueprint-pattern.md).

## Context engineering
The discipline of managing what goes into and out of the context window. Replaced prompt engineering as the central skill in 2026. Five strategies: selection, compression, ordering, isolation, format.

## Context poisoning / distraction / confusion / clash
Drew Breunig's taxonomy of context failures. See [context-engineering/README.md](../context-engineering/README.md).

## FACTS
Dex Horthy's plan-quality criteria: Feasible, Atomic, Clear, Testable, Scoped. Applied to tasks in the plan phase of QRSPI / GSD.

## FAR
Dex Horthy's research-quality criteria: Factual, Actionable, Relevant. Applied to the research phase output.

## Fluid Compute
Vercel's default runtime model (replaced per-request serverless). Reuses function instances across concurrent requests, reducing cold starts. Relevant when agents deploy to Vercel.

## GSD
"Get Shit Done." The phase-based Claude Code plugin I use as my default workflow. Discuss → research → plan → execute → verify → ship. See [workflows/gsd.md](../workflows/gsd.md).

## Hook
A shell command the Claude Code harness runs at a lifecycle event. Used to enforce invariants that shouldn't depend on the model remembering. See [primitives/hooks.md](../primitives/hooks.md).

## MCP (Model Context Protocol)
Open standard for connecting AI agents to tools, data sources, and services. Anthropic-originated, donated to the Linux Foundation (Agentic AI Foundation). See [primitives/mcp.md](../primitives/mcp.md).

## Minions
Stripe's internal coding agents. Fork of Block's Goose. Canonical example of agentic engineering at production scale. See [case-studies/stripe-minions.md](../case-studies/stripe-minions.md).

## One-shot / one-shottable
A task the model can complete in a single invocation without iteration. Bigger context windows have made more tasks one-shottable. Contrast with Ralph Loop.

## Orchestrator ↔ Worker
The baseline multi-agent shape: one orchestrator dispatches isolated workers, reads their summaries, composes. See [patterns/orchestrator-worker.md](../patterns/orchestrator-worker.md).

## Planner / Generator / Evaluator
Anthropic's three-agent harness pattern. A separate evaluator prevents self-review bias. See [patterns/planner-generator-evaluator.md](../patterns/planner-generator-evaluator.md).

## QRSPI
Question → Research → Structure → Plan → Implement. Dex Horthy's 2026 evolution of the RPI pattern. See [patterns/qrspi.md](../patterns/qrspi.md).

## Ralph Loop
Geoffrey Huntley's tight `while :; do claude -p < PROMPT.md; done` loop. See [patterns/ralph-loop.md](../patterns/ralph-loop.md).

## Rule (project rule)
A topical markdown file in `.claude/rules/` loaded on demand when a topic comes up. Prevents the 500-line-CLAUDE.md anti-pattern. See [primitives/commands-and-rules.md](../primitives/commands-and-rules.md).

## Skill
A reusable capability the agent can invoke by name. Defined in `SKILL.md` with frontmatter. Uses 3-stage progressive disclosure. See [primitives/skills.md](../primitives/skills.md).

## Sub-agent
A specialized assistant with its own system prompt, tool allowlist, and conversation history. Used for isolation, specialization, and context scoping. See [primitives/subagents.md](../primitives/subagents.md).

## Worktree
A second git checkout pointing at a different branch, sharing the same object database. Used for parallel isolation — features, experiments, sub-agent runs. See [workflows/worktrees.md](../workflows/worktrees.md).
