# Claude Code

> The harness I use day to day. An Anthropic CLI that has grown into a full agent platform. Release cadence is fast — features come and go. Treat anything below as "rough as of when I wrote this," not as a contract.

## What it actually is

Claude Code is an interactive terminal agent with:

- **Model access** to the Claude family. Exact model names and context window sizes change frequently; the current options are always listed in the Claude Code docs.
- **Tool use** — Read, Edit, Write, Glob, Grep, Bash, plus any MCP servers you install.
- **Primitives** — skills, hooks, sub-agents, commands, rules, plugins (see [primitives/](../primitives/README.md)).
- **Modes** — interactive, headless (`claude -p` for non-interactive use), and various experimental features that come and go.

The CLI is the delivery vehicle. The leverage comes from how you configure it.

## The primitives that matter

| Feature | What it gives me |
|---------|------------------|
| **Skills** | Reusable capabilities with progressive disclosure — metadata in context always, full body loaded on demand, supporting files loaded only when referenced. See [primitives/skills.md](../primitives/skills.md). |
| **Hooks** | Shell scripts that run at lifecycle events (PreToolUse, PostToolUse, SessionStart, Stop, etc.). Deterministic control over formatting, safety, and invariants. See [primitives/hooks.md](../primitives/hooks.md). |
| **Sub-agents** | Specialized assistants with their own system prompts, tool allowlists, and isolated contexts. See [primitives/subagents.md](../primitives/subagents.md). |
| **Commands** | Slash commands that run multi-step workflows. See [primitives/commands-and-rules.md](../primitives/commands-and-rules.md). |
| **Rules** | Topic-specific knowledge loaded on demand instead of stuffed into a mega-CLAUDE.md. |
| **Plugins** | Shareable bundles of the above. The Claude Code plugin marketplace has grown quickly — worth browsing before building your own primitives. |
| **MCP integration** | First-class Model Context Protocol support for connecting to external systems. See [primitives/mcp.md](../primitives/mcp.md). |

## The Claude Agent SDK

Anthropic has packaged the underlying agent engine as a standalone SDK — Python and TypeScript — so you can embed the same tool-use loop in your own applications, CI pipelines, or non-interactive workflows. Look it up in the Anthropic docs; name and exact install command are likely to drift, so I'd rather point you at the current docs than memorize a command that'll be wrong in a month.

The useful pattern: anything you can do interactively in Claude Code, you can do programmatically via the SDK. That opens the door to headless jobs, CI-integrated agents, and embedding agentic workflows inside your own product.

## What I've settled on (personally)

- **Permission mode**: permissive by default (let the model drive), with hooks catching the dangerous stuff.
- **Hooks baseline**: pre-bash safety, pre-bash typecheck, post-edit lint. Everything else is project-specific.
- **Plugins**: workflow plugins (GSD or similar), vendor plugins where relevant, a review/lint plugin, and whatever MCP servers match the stack I'm in (Linear, Notion, Figma, browser, etc.).
- **Rules over mega-CLAUDE.md.** Domain-specific knowledge lives in topic files, loaded on demand.
- **Memory for durable context, not for transient state.** Feedback, preferences, and project-level facts that outlive a single session.

## What I don't use much

- **Experimental multi-agent features** — they come and go, and they're worth trying when a specific task has an obvious wall-clock benefit. For most work, plain sub-agents are fine.
- **Hosted/managed agent offerings** — useful if you need long-running sessions with your laptop closed, but my workflows are tightly coupled to my local environment.
- **Computer Use / browser-driving modes** as a daily driver — too high-latency for interactive work. I reach for them inside evaluator sub-agents that are specifically driving a browser.

## The watchpoint

The biggest risk with a harness that ships this fast is **tooling sprawl** — a new primitive every week, every one promising to be The Thing. My rule: wait two weeks before adopting anything new. If it's still obviously useful after two weeks, integrate. If not, it wasn't.

## Where to read current info

- The official Claude Code documentation — authoritative on features, primitives, and release notes.
- Anthropic's changelog — the cleanest source for what actually shipped when.
- The `anthropics/skills` and plugin marketplace repositories — good examples to learn from.

I've deliberately avoided putting version numbers and specific pricing here because they'll rot fast. Check the official sources when you need a current answer.
