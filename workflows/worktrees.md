# Worktrees

> Parallel isolation that beats stashing and WIP commits for both humans and agents.

A git worktree is a second checkout of the same repo pointing at a different branch. Multiple worktrees, one shared object database. Fast to create, cheap to throw away.

## Why they matter for agent work

Sub-agents can run destructive operations — large refactors, codemods, dependency bumps — in an isolated worktree. If the run goes badly, I `rm -rf` the directory. If it goes well, I merge the branch. Either way, my main checkout is untouched.

Worktrees also unlock **genuine parallelism**. Three agents running in three worktrees don't step on each other's files. They can't accidentally edit the same line at the same time. No lockfiles to fight over.

## My setup

I keep a small wrapper script (`./scripts/wt`) that handles the ceremony:

```bash
./scripts/wt add feature/my-feature     # creates ~/source/<repo>-feature-my-feature/
./scripts/wt list
./scripts/wt remove feature/my-feature
```

Behind the scenes it:

1. Creates the worktree.
2. Copies `.env` files from the main worktree (so the new checkout is runnable).
3. Runs `pnpm install` (or equivalent).
4. Installs git hooks.
5. Is picked up by a `WorktreeCreate` Claude Code hook that runs any additional setup (DB linking, extra env files, etc.).

## Agent worktrees

Sub-agents can be configured with `isolation: worktree` in their frontmatter:

```markdown
---
name: experimental-refactor
isolation: worktree
---
```

This tells the harness: *when you spawn this sub-agent, do it in a fresh temporary worktree.* Cleanup is automatic — if the sub-agent makes no changes, the worktree is removed; if it does, the path and branch come back in the result so I can decide whether to merge.

Starting Claude Code itself in a worktree works too:

```bash
claude --worktree feature-name
```

## Rules I enforce

- **Only one worktree runs `pnpm dev` / `supabase start` at a time.** Port conflicts will ruin your day.
- **Turbo cache is shared** across worktrees — build once, benefit everywhere.
- **Clean up promptly** after merging. Orphan worktrees accumulate fast.
- **Never use worktrees as a backup strategy.** They're for parallel work, not for hoarding old state.

## When worktrees help most

- **Running a Ralph Loop.** Point the loop at a worktree so a bad overnight run is just a deleted directory.
- **Parallel features.** Three small features in three worktrees. Main stays clean.
- **Experimental refactors.** "Try this restructure and I'll decide in the morning."
- **Agent sub-tasks with write access.** Scope the blast radius to the worktree.

## When they're overkill

- Small edits that merge in minutes.
- Anything where the branch lifetime is shorter than the time to set up the worktree.
- Pair-programming sessions where context-switching between checkouts is more expensive than the isolation it buys.

## Source

`git worktree` man page; Claude Code `WorktreeCreate` hook docs; general prior art from Stripe's per-agent VM pattern (same idea, simpler execution).
