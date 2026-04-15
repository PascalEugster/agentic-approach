# Orchestrator ↔ Worker

> The baseline hierarchical shape of multi-agent work. One main context dispatches isolated sub-agents with filtered tool access, receives their results, and composes. Nothing fancy — but it's the default for a reason.

## The shape

```
           ┌──────────────────────┐
           │    Orchestrator       │
           │  (main context)       │
           └──────────┬───────────┘
                      │ spawn
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   ┌─────────┐  ┌─────────┐  ┌─────────┐
   │ Worker  │  │ Worker  │  │ Worker  │
   │ (fresh  │  │ (fresh  │  │ (fresh  │
   │ context)│  │ context)│  │ context)│
   └────┬────┘  └────┬────┘  └────┬────┘
        └────────────┴────────────┘
                     │
                     ▼
                  result
```

Each worker starts cold, runs with its own tool allowlist, writes its output to disk, and returns a short summary. The orchestrator reads the summary, not the worker's full conversation.

## Why isolation matters

The orchestrator's context is expensive — it's the one I actually interact with. If every sub-task pollutes it with raw grep output, file reads, and exploratory detours, I lose the ability to steer the session. Workers absorb the noise.

The principle of least authority applies: each worker gets only the tools it needs. A code-review worker doesn't need Write. A deploy worker doesn't need Glob over the whole repo. Narrower tool sets reduce blast radius and improve decision quality — fewer options means less paralysis.

## When I use it

- **Wide reads.** Mapping a codebase, searching for patterns across many files, ingesting long docs. Always a worker.
- **Parallel independent work.** Running three codemods that don't touch each other. Spawn three workers in worktrees.
- **Specialized skills.** Code review, UI audit, test generation, debugging — each has its own worker type with its own system prompt.
- **Anything I'd rather not read the chain-of-thought of.** If the output is what matters and the exploration is noise, it belongs in a worker.

## When I skip it

- Small, conversational tasks where delegation overhead > the task itself.
- Anything where the orchestrator's judgment in the middle is load-bearing — you can't pause a worker to debate an approach.

## Tool filtering in practice

In Claude Code, a sub-agent's allowlist lives in its frontmatter or is passed at spawn time. My rough templates:

| Worker type | Tools allowed |
|-------------|---------------|
| Researcher | Read, Glob, Grep, WebFetch, WebSearch, (MCP read-only) |
| Code reviewer | Read, Glob, Grep, Bash (test/lint only) |
| UI auditor | Read, Bash (browser MCP), image-capable |
| Executor | Read, Edit, Write, Bash, Glob, Grep |
| Debugger | Read, Bash, Grep, Glob, Write (state files only) |

The executor is the only one with broad Write, and it only runs inside approved plans.

## The failure mode

The main failure I see is the orchestrator treating a worker like a chat — back-and-forth micromanaging. At that point you've lost the isolation benefit. Either the task is small enough to do inline, or it's big enough to spec clearly and hand off. Pick one.

## Source

General multi-agent literature — LangGraph 2.0's Router/Supervisor/Subagent primitives, Anthropic's orchestrator-worker docs, and Addy Osmani's *The Code Agent Orchestra* all converge on this shape.
