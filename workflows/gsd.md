# GSD — Get Shit Done

> My default workflow for feature work. Phase-based, artifact-driven, sub-agent-heavy. Lives as a Claude Code plugin with its own command namespace (`/gsd:*`).

## What GSD gives me

- A project file (`.planning/PROJECT.md`) describing the product.
- A roadmap (`.planning/ROADMAP.md`) organized into phases.
- A phase directory per feature (`.planning/phases/NN-feature-name/`) with DISCUSS, RESEARCH, PLAN, VERIFICATION documents.
- A set of specialist sub-agents — researcher, planner, plan-checker, executor, verifier, UI auditor, integration checker.
- Commands that orchestrate the phases without me having to manually invoke each agent.

All artifacts live on disk. All phases run in fresh sub-agent contexts. My main session stays cheap and focused.

## What a session actually looks like

To make this less abstract — a typical flow when I sit down to add a small feature, call it "user profile pages":

```
me:   /gsd:discuss-phase "user profile pages — view + edit"
agent: [asks 4–5 targeted questions: which fields? who can see? admin override?]
me:   [answers each]
agent: writes .planning/phases/07-user-profiles/DISCUSS.md, shows me the summary
me:   /gsd:plan-phase
agent: spawns a researcher sub-agent → writes RESEARCH.md
agent: spawns a planner sub-agent → writes PLAN.md (tasks, dependencies, verification)
agent: spawns a plan-checker sub-agent → approves or flags issues
me:   [read the plan, approve, or iterate]
me:   /gsd:execute-phase
agent: executes task-by-task, commits atomically, hooks lint/typecheck after every edit
me:   /gsd:verify-work
agent: goal-backward check, writes VERIFICATION.md
me:   /gsd:ship
agent: opens PR, runs review, prepares merge
```

Each of those commands runs a separate sub-agent in a fresh context. My main session only sees the summaries and the artifacts. By the time I'm reviewing the PR, four or five specialist agents have each touched the work in their own scope — none of them polluted my main session.

## The phases

### Discuss

> `/gsd:discuss-phase`

An adaptive questioner surfaces assumptions before a line of code is written. Out comes `DISCUSS.md` — goal, constraints, approach, open questions. Optional `--auto` flag picks recommended defaults without the interactive loop.

**What it prevents:** implementing the wrong thing because nobody named the ambiguity.

### Research

> `/gsd:research-phase` (usually runs implicitly as part of plan-phase)

A researcher sub-agent pulls in external docs, similar prior work, API references, and writes `RESEARCH.md` using FAR criteria: *Factual, Actionable, Relevant*. This runs in its own context — I never see the 40 doc pages, only the synthesis.

**What it prevents:** planning against a stale mental model.

### Plan

> `/gsd:plan-phase`

The planner sub-agent reads `DISCUSS.md` + `RESEARCH.md` and writes `PLAN.md` with FACTS tasks: *Feasible, Atomic, Clear, Testable, Scoped*. A plan-checker sub-agent reviews it before I approve.

**What it prevents:** a plan that sounds good but can't actually be executed.

### Execute

> `/gsd:execute-phase`

The executor works plan tasks one at a time with atomic commits. Deviation handling, checkpoint protocols, state tracking. Hooks fire on every Edit / Bash call — lint, typecheck, safety. Can run multi-wave with parallel workers where task dependencies allow.

**What it prevents:** half-finished tasks, skipped lint, unsafe commands sneaking through.

### Verify

> `/gsd:verify-work` / `verifier` sub-agent

Goal-backward verification: *does the code actually deliver what the phase promised, not just what the tasks said?* Writes `VERIFICATION.md`. Separately, if it's a frontend phase, `/gsd:ui-review` runs a 6-pillar visual audit with screenshots.

**What it prevents:** "tests pass" being mistaken for "goal met."

### Ship

> `/gsd:ship`

Opens the PR, runs review (separate code-quality reviewer), prepares the merge. Integration checker verifies cross-phase flows still work end-to-end.

## The shape on disk

```
.planning/
├── PROJECT.md          # Product + context
├── ROADMAP.md          # Phase list with status
├── STATE.md            # Current state machine
├── REQUIREMENTS.md     # Cross-phase requirements
├── phases/
│   ├── 01-auth-basics/
│   │   ├── DISCUSS.md
│   │   ├── RESEARCH.md
│   │   ├── PLAN.md
│   │   └── VERIFICATION.md
│   └── 02-user-profiles/
│       └── ...
├── archive/
├── codebase/           # Map-codebase output
└── research/           # Cross-phase research
```

## Why this works

1. **Every phase runs in a fresh context.** No cross-phase context pollution.
2. **Artifacts are on disk, not in chat.** I can come back in a week and everything I need is in `.planning/`.
3. **Sub-agents do the heavy reading.** My main session stays steerable.
4. **Commands are idempotent.** Re-running `/gsd:plan-phase` is safe — it just regenerates the plan from current state.
5. **Hooks enforce invariants at the execution layer.** The plan doesn't have to remember to type-check; the hook makes it happen.

## When I use lighter workflows

| Command | When |
|---------|------|
| `/gsd:fast` | Trivial one-liner. No ceremony. |
| `/gsd:quick` | Small task, still wants atomic commits + state. |
| `/gsd:debug` | Different loop — scientific-method debugging with persistent state across resets. |
| `/gsd:new-project` | Cold-start a project with deep context gathering and PROJECT.md. |
| `/gsd:new-milestone` | End-of-milestone → new roadmap cycle. |

## Source

GSD plugin (Get Shit Done) for Claude Code. The phase model is heavily influenced by Dex Horthy's QRSPI pattern and Anthropic's Planner/Generator/Evaluator harness work.
