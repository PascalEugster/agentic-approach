# Agent Frameworks

Libraries for building agents yourself (as opposed to harnesses you run out of the box). When a harness like Claude Code isn't enough — because you need to embed an agent in your own app, or orchestrate a custom workflow — these are the usual picks.

## The short list

| Framework | Shape | Strengths |
|-----------|-------|-----------|
| **LangGraph** (LangChain) | Directed graph | Checkpointing, debugging, mature tracing via LangSmith |
| **OpenAI Agents SDK** | Handoffs / tools | OpenAI ecosystem integration, durable execution options |
| **Claude Agent SDK** | Tool-use loops | OS-level access, coding workflows, shared engine with Claude Code |
| **Google ADK** | Hierarchical tree | Multi-language support, Vertex AI integration |
| **CrewAI** | Role-based crews | Fast prototyping, visual editor, strong community |
| **n8n** | Workflow DAG with agent nodes | Self-hosted option, large integration library, MCP support |

Relative production maturity shifts constantly. Rather than try to rank these, I'd pick based on the *shape* that fits your problem and check each project's current status before committing.

I've deliberately **left out** some frameworks that were prominent a year or two ago but have since moved into maintenance mode or been absorbed into other projects. If you're evaluating agent frameworks, check the project's recent release activity before adopting — a stale repo is a warning sign.

## LangGraph

One of the most mature multi-agent frameworks. Explicit graph model — nodes, edges, state, checkpoints. Recent versions have codified common orchestration patterns (routers, supervisors, sub-agents) into reusable primitives. Pairs with LangSmith for observability, which matters more than it sounds — debugging a multi-agent system without tracing is like debugging a distributed system with `printf`.

When I'd pick it: custom agent pipelines where I need deterministic state transitions, retries, and durable execution. Embedded in a product, not running on my laptop.

## OpenAI Agents SDK

The successor to the older OpenAI function-calling patterns. Handoffs are the interesting primitive — one agent passing a task to another with explicit context filtering. Integrates with Temporal for durability. Strong if you're OpenAI-first.

## Claude Agent SDK

The Claude Code engine, packaged for programmatic use. `pip install claude-agent-sdk`. Same tools (Bash, Read, Write, Edit, Glob, Search), now callable from Python or TypeScript. Per-session `max_budget_usd` to prevent runaway spend. Pairs with Claude Managed Agents if you want Anthropic to host the sandbox, checkpoint, and run multi-hour sessions.

When I'd pick it: you like the Claude Code model but need to embed it in a product, a CI pipeline, or a non-interactive flow.

## n8n

Less "framework" and more "visual workflow orchestrator with agent nodes." Worth listing because for small teams it's often the easiest way to wire multi-agent work to real systems. Highlights:

- **Self-hosted option** — sensitive workloads can stay on your infrastructure, which matters if you're in a regulated environment.
- **Distributed workflow execution.**
- **MCP support** — can act as a client for MCP tools.
- **Large library of third-party integrations** — the long tail of SaaS connectors you'd otherwise hand-roll.

When I'd pick it: recurring multi-step agent workflows that touch many external systems (CRM, email, project management, etc.), where I'd rather drag-and-drop than write glue code.

## CrewAI

"Role-based multi-agent" framework — describe a crew, assign roles, let them collaborate. Good for prototyping, useful for reasoning-heavy exploratory tasks, historically weaker on production durability than graph-based frameworks. Has significant community momentum and a visual editor.

When I'd pick it: prototyping multi-agent conversations to find the right decomposition before formalizing the result in a more production-hardened framework like LangGraph.

## Two adjacent categories worth knowing

### Durable execution

Modern agentic workloads increasingly need **durable execution**: the ability to survive process crashes, resume from checkpoints, retry failed steps, and run for hours without losing state. This is a specialized problem, and most agent frameworks either delegate it or build on a durable-execution engine underneath.

Tools in this space include Temporal (language-agnostic, widely used), Inngest, and newer platform-native offerings like Vercel's Workflow DevKit. If you're building a production agent that needs to pause for external events, retry with backoff, or run beyond a single request lifecycle, look here before hand-rolling state management.

### Agent evaluation and observability

The other category most teams underestimate until it's too late: **evaluating** what your agents actually do over time. "It worked on my laptop" isn't a strategy when you have thousands of runs a week.

Look at tooling like Braintrust, Langfuse, LangSmith (paired with LangGraph), or Arize Phoenix for tracing, evaluation, and regression detection on agent outputs. Even lightweight internal dashboards beat nothing. If you don't have observability on your agent runs, you don't know whether a "fix" helped or hurt — and you will accidentally regress.

I don't use any of these heavily myself yet, but I expect this is where my next big harness investment will go.

## The rule

> *Pick a framework when your workflow doesn't fit into a harness. Most workflows fit into a harness.*

I've built maybe two things in my life that actually needed LangGraph. Everything else fit into Claude Code + GSD + a handful of MCP servers. Frameworks are for the cases where you're building a *product* that uses agents internally, not for your own day-to-day coding.

## Source

Each of these frameworks has its own official documentation. Rather than link to specific URLs that may rot, just search the project name — their docs are the authoritative reference for current APIs and features.
