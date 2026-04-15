# Stripe — Minions

> Stripe's internal coding agents. The clearest public example I know of for how agentic engineering at scale actually fits together. Based on a fork of Block's open-source Goose agent.

> **A note on specifics.** Public coverage has included impressive weekly PR counts and dramatic productivity claims. The numbers vary between sources, and I don't want to vouch for any one figure. What matters for this writeup is the *architecture*, which Stripe's engineering blog and The Pragmatic Engineer's deep dives have been transparent about. For the exact stats, go to the original reporting.

## The architecture — what Stripe has been clear about

- **Base**: a fork of Block's open-source Goose agent.
- **Orchestration pattern**: the [Blueprint pattern](../patterns/blueprint-pattern.md) — interleaved deterministic and agentic nodes.
- **Context prefetch**: before any LLM runs, a deterministic orchestrator gathers all the context the agent will need — scanning relevant Slack threads, pulling linked tickets, searching code, identifying affected tests and dependencies.
- **Tool curation**: a small number of relevant tools is selected per task out of a much larger internal tool fleet. The agent doesn't see every available tool — just the ones that matter for this task.
- **Sandboxing**: each agent run gets its own isolated environment.
- **Execution model**: one-shot. No memory between invocations. No iterative self-refinement by the agent.
- **Authority boundary**: **submission authority only, not merge authority.** Agents propose PRs; humans review and merge.

## Why this works — the core insight

The message Stripe's engineering writing keeps coming back to:

> The thing that makes Minions work is not the AI model. It's the engineering infrastructure Stripe built for human engineers, years before LLMs existed — reliable CI, clean dependency graphs, good tests, Sourcegraph for code navigation, a disciplined ticket system.

The agentic layer sits on top of all that. Without it, the agent would be reasoning about noise.

The LLM is the easy part. The hard part — and the part that compounds — is the quality of the deterministic environment around it.

## What the deterministic layer actually does

Roughly, before the agent is even called:

1. **Ticket analysis** — read the issue, its comments, linked tickets, acceptance criteria.
2. **Conversation mining** — pull in the relevant Slack/chat threads that triggered the work.
3. **Code search** — find relevant files, test files, call sites.
4. **Tool curation** — pick the small set of tools relevant to this task out of the larger fleet.
5. **Environment prep** — spin up an isolated sandbox.
6. **Hand-off** — *now* the agent is invoked, with a tight context and a focused task.

By the time the LLM runs, most of the "thinking" about what to do has already happened deterministically. The agent's job is narrow: "given all this, write the code."

## One-shot execution

Minions don't loop. They don't iterate on their own output. They don't remember prior invocations. Each run is a complete, isolated attempt.

Why? Because iterative self-refinement is where agents get into trouble. Error modes compound. One-shot keeps the blast radius small and the behaviour predictable. If a Minion fails, a human looks at it — we don't ask the Minion to retry.

## The submission authority boundary

> **Submission authority. Not merge authority.**

Every PR goes through standard human code review. The agents propose; humans dispose. This single boundary is what makes high-throughput agent workflows tolerable — the organization hasn't delegated trust, just increased the rate of proposals.

## What's transferable

- **Prefetch context deterministically before the LLM runs.** Hooks can do this on a small scale. Reach for code + docs + relevant references *before* invoking the agent, not during.
- **Curate tools, don't expose them all.** My version: sub-agents with filtered `tools:` allowlists.
- **Isolate per-task.** My version: git worktrees. Not as clean as a fresh VM, but the principle holds.
- **One-shot where possible.** If I can specify a task clearly, I don't loop. Loops are for cases where I genuinely can't specify the end state.
- **Never give agents merge authority.** All agent output goes through review. No exceptions.

## The rule I extracted

> *When an agent produces bad output, the first suspect is the context, not the model. Usually the fix is a better deterministic layer around the agent — not a smarter prompt.*

I've been surprised how often this turns out to be right.

## Where to read more

- Stripe's engineering blog
- *The Pragmatic Engineer* deep dives on Stripe's AI tooling adoption
- Block's Goose: `github.com/block/goose`
