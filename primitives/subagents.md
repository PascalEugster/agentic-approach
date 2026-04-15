# Sub-agents

> If it needs its own context window, it's a sub-agent.

A sub-agent is a specialized assistant with its own system prompt, its own tool allowlist, and its own conversation history. Lives in `.claude/agents/` (project) or `~/.claude/agents/` (user).

## The anatomy

```markdown
---
name: code-quality-reviewer
description: Reviews code for adherence to project guidelines, style, and best practices. Use proactively after writing or modifying code.
tools: Read, Glob, Grep, Bash
model: sonnet
---

# Code Quality Reviewer

System prompt for the reviewer agent. Describe the role, the review criteria,
how to format output, when to be strict, when to be lenient...
```

The `description` field is the targeting hint — the main agent decides whether to delegate based on it. The `tools` list is the allowlist (least authority). `model` can override the default.

## Why not just prompt inline?

Three reasons, in order of importance:

1. **Context isolation.** The sub-agent's exploration doesn't pollute my main session. A reviewer that reads 40 files returns a one-paragraph verdict; I never see the 40 file-reads.
2. **Specialized system prompt.** A dedicated "this is a strict reviewer that doesn't accept lenient judgments" system prompt produces measurably different output than the same instructions appended to a general session.
3. **Tool scoping.** A reviewer doesn't need Write. A debugger doesn't need Glob over the whole repo. Narrower tools → better decisions.

## The sub-agents I actually use

| Agent | Job | Tools |
|-------|-----|-------|
| `code-quality-reviewer` | Reviews diffs against project rules before commit | Read, Glob, Grep, Bash (lint/test only) |
| `gsd-planner` | Turns a discuss doc into a phase plan | Read, Write, Glob, Grep, WebFetch |
| `gsd-executor` | Executes a plan task-by-task with atomic commits | Read, Write, Edit, Bash, Glob, Grep |
| `gsd-verifier` | Goal-backward verification of a completed phase | Read, Bash, Grep, Glob, Write |
| `gsd-phase-researcher` | Pulls external docs before planning | Read, Write, Bash, WebFetch, WebSearch, MCP (read-only) |
| `gsd-ui-auditor` | 6-pillar visual review of frontend code | Read, Bash (browser MCP), Grep, Glob |
| `gsd-debugger` | Scientific-method debugging with persistent state | Read, Write, Edit, Bash, Grep, Glob |

Most of these are shipped by the GSD plugin — I didn't hand-write them, but they're worth listing because they show the division-of-labour pattern.

## Worktree isolation

Sub-agents can be configured to run in their own git worktrees:

```markdown
---
name: experimental-refactor
isolation: worktree
---
```

This gives the sub-agent a physically separate checkout so it can edit files without touching my main work. If the run goes badly, the worktree is deleted. If it goes well, I merge. Cheap to throw away, cheap to keep.

## When to delegate

- Broad reads (mapping, grepping, long doc ingestion).
- Specialized judgment (review, audit, verification).
- Parallelizable independent work.
- Anything whose output is a verdict or a document, not a conversation.

## When not to delegate

- Small, interactive tasks where delegation overhead is more than the task.
- Anything where I'd want to intervene mid-run.
- Cases where the sub-agent would need the same context I already have — then just do it inline.

## Source

Claude Code sub-agents docs: code.claude.com/docs/en/agents. `anthropics/claude-code-templates` on GitHub has good example agents.
