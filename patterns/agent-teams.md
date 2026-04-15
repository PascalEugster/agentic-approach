# Agent Teams

> A pattern for peer-to-peer multi-agent collaboration — sub-agents that can talk directly to each other, not just back to the orchestrator. Claude Code has shipped experimental features in this direction; exact feature names and flags change between releases, so check the current docs if you want to try it.

## What changes vs. plain sub-agents

| | Sub-agents | Agent Teams |
|---|------------|-------------|
| Communication | Orchestrator ↔ worker, hub-and-spoke | Peer-to-peer via a mailbox system |
| Shared state | Via orchestrator | Shared task list, mutual awareness |
| Coordination | Explicit hand-offs | Teammates discover each other's work |
| Analogy | Contractors you send on errands | A project team sharing a room |

The mailbox is the key primitive. Teammate A can send a message directly to Teammate B ("I'm blocked on X, you own it, can you confirm?") without routing back through the orchestrator. Task dependencies get tracked automatically.

## Performance profile

The trade is straightforward: meaningful wall-clock speedup on parallelizable work, at a meaningful token-cost premium. You're paying more tokens for less waiting. For anything where I'm waiting on the clock (pre-demo crunch, blocked on a review), the trade makes sense. For background work where I don't care about wall-clock, plain sub-agents are fine.

Keep the team small. The coordination overhead rises quickly with the number of participants — more than a handful of agents and the "team" spends more time talking than working.

## Where it actually helps

- **Parallel features that need to converge.** Three sub-agents building three related components, each one blocking on someone else's interface. Teams let them negotiate interfaces without the orchestrator brokering every exchange.
- **Review + fix loops.** A reviewer teammate and a coder teammate working against the same task list, catching issues early.
- **Cross-cutting refactors.** One teammate owns the codemod, another owns the tests, a third owns the docs. They move in parallel and ping each other when a rename lands.

## Where I'm still cautious

- It's experimental. The coordination primitives can deadlock on bad task decomposition.
- Token cost is real. A 5-agent team for a 30-minute task is burning a lot of tokens on coordination overhead.
- Observability is rough — debugging "why did Teammate B not pick up the message" is harder than debugging a single agent.
- Shared task lists can get stepped on if two teammates update the same row.

## My current rule

Enable Agent Teams for wall-clock-sensitive parallel work. Keep plain sub-agents as the default everywhere else. When in doubt, one orchestrator + two sub-agents beats five teammates arguing about who owns what.

## Source

Claude Code's own release notes and changelog are the authoritative place to check what's currently shipping. The general pattern of peer-to-peer multi-agent coordination is discussed in several public talks and in multi-agent framework literature (LangGraph's supervisor/router primitives cover a similar shape).
