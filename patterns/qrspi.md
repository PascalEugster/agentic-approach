# QRSPI — Question / Research / Structure / Plan / Implement

> A phase-based evolution of the earlier Research-Plan-Implement shape associated with Dex Horthy and HumanLayer. I'm using "QRSPI" as a working label here for the Q + R + S + P + I sequence I actually run. Treat the name as shorthand; the value is in the phases, not in the acronym.

## The problem with plain RPI

RPI's research phase used a single sub-agent given ~40% of the context window and a broad prompt. In practice this turned out to be unreliable — too much context, too little structure, too easy for the planner to ignore or misread the research output.

## What QRSPI adds

A **Structure** step between Research and Plan. It forces a ~200-line markdown "design discussion" document that acts as an explicit alignment layer between me and the agent before any code is written. No code happens until the structure doc reads right.

The five phases:

| Phase | What happens | Output |
|-------|--------------|--------|
| **Question** | Clarify what we're actually building and why. Surface assumptions. | Short goal + constraints doc |
| **Research** | Sub-agent in fresh context pulls in docs, code, examples. FAR criteria: *Factual, Actionable, Relevant*. | `RESEARCH.md` |
| **Structure** | Design discussion as markdown — module boundaries, data flow, open questions. My sanity check. | `STRUCTURE.md` (~200 lines) |
| **Plan** | Task breakdown with FACTS criteria: *Feasible, Atomic, Clear, Testable, Scoped*. | `PLAN.md` |
| **Implement** | Execute plan, fresh context per task where needed. | Code + commits |

## Why each phase gets a fresh context window

This is the non-obvious part. Each phase is a separate sub-agent invocation with its own context. Research findings are written to disk; the planner reads them but doesn't inherit the researcher's full conversation. This is the only way to keep planning coherent on multi-day features — otherwise the context window poisons itself with exploration noise.

## Why Structure matters more than it sounds

LLMs are "pathological optimists" about their own plans. Without a forcing function that makes design decisions *explicit and reviewable before code*, the agent gets creative during implementation — especially at the boundary between frontend and backend, where data-model assumptions quietly drift. A 200-line structure doc I actually read catches 80% of these drifts before they become PRs.

## How this maps to GSD

GSD's `discuss-phase → research-phase → plan-phase → execute-phase → verify-phase` is essentially QRSPI with Question and Structure fused into the discuss phase and an additional verifier on the back end. Same shape, slightly different ergonomics.

## When I reach for it

- New feature crossing more than one package boundary.
- Anything touching the data model.
- Anything where "I'm not sure what the right architecture is yet."
- Anything I'd otherwise be tempted to one-shot and regret in review.

## When I skip it

- Trivial fixes and copy changes.
- Mechanical refactors where the shape is already clear.
- Anything where Structure would be longer than the code it describes.

## Source

The phase-based "research separately, plan separately, implement separately" shape comes from Dex Horthy and HumanLayer. The specific labels ("QRSPI," "FAR," "FACTS") as presented here are my own shorthand for the moves I actually make — I can't promise they line up exactly with any particular talk. If you want the current canonical writeup, check HumanLayer's blog and Horthy's public talks directly.
