# When to use what

A quick decision table. Bookmark this and re-read it every few weeks — my defaults keep drifting, and I have to catch myself.

## Task size → workflow

| Size | Example | Workflow |
|------|---------|----------|
| **XS** | Typo, copy change, one-line config fix | Inline chat. No ceremony. |
| **S** | Rename a prop, tighten a type, add an env var | `/gsd:fast` or inline. |
| **M** | A focused bug fix, a small component, one utility function | `/gsd:quick` — atomic commits and state, no plan phase. |
| **L** | A feature spanning ≥2 files, a migration, UI + data work | Full GSD: discuss → plan → execute → verify. |
| **XL** | Multi-day build, data model change, cross-package refactor | Full GSD + QRSPI structure step + Planner/Generator/Evaluator for big builds with a visual surface. |
| **Overnight** | Migration campaign, codemod across hundreds of files, "grind until green" | Ralph Loop in a worktree with strict hooks. |

## Task shape → pattern

| Shape | Pattern |
|-------|---------|
| Clear spec, one decision | One-shot prompt. Don't over-engineer. |
| Clear spec, many decisions | QRSPI. |
| Fuzzy spec, "I'll know it when I see it" | Planner/Generator/Evaluator with a browser evaluator. |
| Wide read, narrow verdict | Sub-agent with filtered tools. |
| Integration-heavy with deterministic gates | Blueprint — deterministic context prefetch + agentic step. |
| Parallel independent pieces | Orchestrator + worktree-isolated workers. |
| Wall-clock-sensitive parallel | Agent Teams. |

## Context size → intervention

| Context full | Do |
|--------------|----|
| <30% | Relax. Keep going. |
| 30–60% | Start writing long outputs to disk, not chat. |
| 60–80% | Delegate the next read-heavy step to a sub-agent. |
| >80% | Compact, or start a new phase in a fresh context. |
| >90% | Stop. You're getting worse output than you think. |

## Output shape → primitive

| Output | Primitive |
|--------|-----------|
| "Run these steps in order" | Command (`/my-thing`) |
| "A capability the agent can use when needed" | Skill |
| "Enforce this every time" | Hook |
| "Load this knowledge only when the topic comes up" | Rule |
| "A focused judgment with its own context" | Sub-agent |
| "Connect to an external system" | MCP server |
| "All of the above, packaged" | Plugin |

## The honest rule

> *If you can't pick a workflow in ten seconds, the task is probably XL and you should do a discuss phase first.*

Indecision itself is a signal. It means the shape isn't clear yet, and the right move is to spend 15 minutes on alignment instead of 3 hours on a wrong implementation.
