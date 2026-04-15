# Blueprint Pattern

> The architecture Stripe has described for their internal coding agents ("Minions") — a fork of Block's open-source Goose wrapped in Stripe's own orchestration. The Blueprint pattern is the shape; the infrastructure underneath it is where the real moat lives.

## The core idea

A Blueprint is an orchestration flow that alternates between two kinds of nodes:

- **Deterministic nodes** — fixed, predictable operations. Scanning Slack threads, pulling Jira tickets, running Sourcegraph MCP queries, linting, running tests, opening PRs. No LLM in the loop.
- **Agentic nodes** — reasoning and generation. Reading code, writing code, deciding "which of these approaches fits the spec."

The deterministic layer does all the context gathering *before* the LLM runs. Every agentic node starts with a preloaded, pre-filtered view of exactly what it needs.

## Why this is the actual unlock

Stripe curates a small, task-relevant set of tools out of a much larger internal fleet, so the agent never sees "token paralysis." Context is prefetched deterministically: chat/tickets → code search → relevant files → dependencies → tests. By the time the LLM gets called, the hard part is over.

The recurring theme in Stripe's public writing on this: the thing that makes the agent work is not the model — it's the engineering infrastructure that was already in place for human engineers, long before LLMs arrived. Good test coverage, reliable CI, clean dependency graphs, deterministic linters, stable deploy pipelines. The agentic layer sits on top of that work. Without it, you're asking the agent to reason about noise.

## The isolation rule

Each agent run gets its own sandboxed environment, prewarmed so spin-up time isn't a bottleneck. **One-shot execution** — no memory between invocations, no iterative refinement of the agent's own state. The invariant is that nothing an agent does should break anything across invocations. Idempotence by construction.

And crucially: the system has **submission authority, not merge authority**. PRs go through normal human code review. The agent proposes; humans dispose. That single boundary is what makes high-throughput agent workflows tolerable — you can trust the output because you haven't actually delegated trust.

## What this means for a small team

I can't build Stripe's infrastructure. What I can do is apply the principles on a smaller scale:

| Stripe | My version |
|--------|------------|
| Internal MCP fleet | Public MCP servers — Linear, Notion, Figma, browser automation, docs |
| Isolated prewarmed sandboxes | Git worktrees with a `WorktreeCreate` hook setup script |
| Deterministic context prefetch | Hooks + skills that gather context before the agentic step |
| Large internal tool fleet curated to a handful per task | Rules files + scoped sub-agents with filtered tool access |
| Submission-only authority | PRs through standard review, no direct pushes to protected branches |
| CI-integrated invocation | GitHub Actions + headless agent mode (`claude -p` or similar) |

## The rule I've internalized

> *Every time I'm tempted to put an LLM on a problem, ask whether the deterministic layer around it is actually good enough. Usually the answer is "the LLM is fine, but the context it's getting is garbage." Fix the context-gathering before touching the prompt.*

Most "the agent made a mess" failures I've seen are actually context failures in disguise.

## Source

Stripe's engineering blog and *The Pragmatic Engineer* newsletter have both published writeups of the Minions architecture that are worth reading directly. Block's Goose agent is public on GitHub and is a good reference implementation to study if you want to see what an open-source starting point for this kind of work looks like.
