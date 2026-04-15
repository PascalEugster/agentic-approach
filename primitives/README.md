# Primitives

The building blocks Claude Code exposes. Each one solves a specific problem. Together they compound — adding one multiplies the value of the ones already there.

## Index

- [**Skills**](skills.md) — Reusable capabilities. The unit of "I've done this more than twice."
- [**Hooks**](hooks.md) — Lifecycle events. The unit of "this must happen every time, whether the model remembers or not."
- [**Sub-agents**](subagents.md) — Isolated contexts with filtered tool access. The unit of "keep this noise out of my main session."
- [**Commands & Rules**](commands-and-rules.md) — Slash commands and on-demand domain knowledge. The unit of "a workflow I want to invoke, and the rules that apply when I do."
- [**MCP**](mcp.md) — Model Context Protocol. The unit of "first-class access to an external system."

## The decision tree

> **I've explained it twice** → skill.
> **It must happen every time** → hook.
> **It needs its own context window** → sub-agent.
> **I want to invoke it with a slash** → command.
> **I want to load it only when a topic comes up** → rule.
> **I want the agent to talk to an external system** → MCP server.
> **I want all of the above bundled and shared across projects** → plugin.

## Scope

Each primitive can live at three levels:

| Level | Path | Scope |
|-------|------|-------|
| Project | `.claude/` | This repo only |
| User | `~/.claude/` | All my projects |
| Plugin | `~/.claude/plugins/` | Installable, shareable |

As a rough rule, I start at project level, promote to user level once I've used it on two projects, and package as a plugin when a third team member would benefit.
