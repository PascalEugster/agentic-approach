# Ralph Loop

> Geoffrey Huntley's tight prompt-in-a-loop technique, named after Ralph Wiggum. The whole pattern is essentially:
>
> ```bash
> while :; do cat PROMPT.md | claude -p ; done
> ```
>
> Trivial to type, surprisingly effective, still occasionally the right answer.

## What it actually does

A single file (`PROMPT.md`) describes the goal, the current state, and the stop condition. The loop runs one Claude Code invocation at a time in non-interactive mode, re-reading the prompt each iteration. On every pass the agent:

1. Reads `PROMPT.md` and any referenced state files.
2. Makes forward progress.
3. Updates state files so the next iteration knows where to pick up.
4. Optionally exits when a stop marker is reached.

Because each iteration is a fresh context, the loop is immune to context pollution — everything load-bearing lives on disk.

## Why it got less essential in 2026

Opus 4.6 with a 1M-token context window can now one-shot tasks that used to require the loop. The pain the loop was solving — "context window fills up before I finish the refactor" — is partially dissolved by bigger windows and better compaction.

But "partially" is doing real work in that sentence. The loop still wins for:

- **Overnight mechanical work.** Large migrations, dependency updates, codemod campaigns.
- **Grind-until-green tasks.** Run the loop against a failing test suite, goal = "all green, don't touch the public API." Come back in the morning.
- **Long-tail refactors.** Rename X to Y across a monorepo where each rename might cascade.
- **Self-healing pipelines.** Point a loop at CI output, let it chase failures.

## The semi-official status

The loop has picked up enough adoption that various wrapper packages and plugins exist around it. Rather than name specific package identifiers (which rot fast), search the Claude Code plugin marketplace and npm for "ralph" if you want a ready-made version — or just copy the three-line shell loop into your terminal, which is the whole pattern. Huntley has also written about larger multi-loop orchestration ideas, which are worth reading directly.

## How I actually run it

Only on tasks where I'm comfortable letting the agent make mistakes and clean them up — never on production-adjacent code without a hook guardrail that blocks destructive commands. Typical setup:

- `PROMPT.md` with explicit STOP marker and a "do not touch these files" list.
- A pre-bash safety hook to block `rm -rf`, force-push, DB drops.
- A post-edit lint hook so each iteration leaves the code in a formatted state.
- A wall-clock cap or max-iteration count on the loop shell itself.
- Run it in a git worktree, not main, so a bad run is just a deleted directory.

## When I skip it

- Anything where the "right answer" is one decision, not many. A loop is wasted on single-decision work.
- Anything production-facing without strong hooks and a kill switch.
- Anything where I'd rather see each step and approve it.

## Source

Geoffrey Huntley's writing on `ghuntley.com`. The loop has been discussed in various forms over the last year or two; search his blog for the current canonical version and follow-up concepts.
