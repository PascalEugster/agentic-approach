# MCP — Model Context Protocol

> First-class access to an external system without hand-rolling an API wrapper.

MCP is a standard protocol for connecting AI agents to tools, data sources, and services. Anthropic originated it and released it as an open standard. Since then, adoption has grown fast — both in the number of available servers and in the set of clients that speak it — and the protocol has moved toward more formal governance. Major vendors across the AI and cloud space have adopted it or committed to it. For current stats and governance status, check `modelcontextprotocol.io` directly; the numbers move quickly.

## The mental model

> *LLM is the CPU. Context window is RAM. MCP is the peripheral bus.*

An MCP server exposes a set of tools, resources, and prompts to any MCP-compatible client. Claude Code, Cursor, Codex, and most serious agent harnesses all speak it. Once a server is installed, its capabilities are just… available, the same way a tool you wrote yourself would be.

## The servers I actually use

| Server | What I use it for |
|--------|-------------------|
| **Linear** | Read/write issues, statuses, comments, cycles, projects. The agent can actually run the issue tracker, not just reference it. |
| **Notion** | Read/write docs and databases. Good for project context that lives in wiki-shaped places. |
| **Figma** | Read design files, extract components, generate code from frames. The design → code gap narrows meaningfully. |
| **Next.js devtools** | Inspect component trees, hooks, props, PPR shells, errors, network — what React DevTools gives humans, the agent gets as commands. |
| **Browser / Playwright** | Drive a real browser: navigate, click, fill forms, screenshot, diff, verify. Essential for the Evaluator role in the Planner/Generator/Evaluator pattern. |
| **Supabase** | Schema introspection, query execution, migrations. |
| **Context7** | Up-to-date library documentation. Replaces "hope training data is current." |
| **Firecrawl / Exa** | Web research with structured extraction. |
| **Vercel** | Deployment and project management through the agent. |

## A2A vs. MCP

Two adjacent protocols, often confused:

- **MCP** — agent ↔ tool. How an agent talks to external systems.
- **A2A** (Google-originated) — agent ↔ agent. How agents coordinate across organizational or vendor boundaries.

I use MCP constantly. I've touched A2A only experimentally. For most day-to-day coding work, MCP is the one that matters.

## The security question

Every MCP server is a foreign capability added to my agent. That raises real questions:

- **Scope.** What can this server read? What can it write? Does it have org-wide access or user-scoped access?
- **Credentials.** Where does the token live? How long does it live? How do I rotate it?
- **Auditability.** Can I see what the agent called and when?
- **Prompt injection surface.** Any data the MCP server returns is untrusted text that can try to redirect the agent.

I default to: read-only scopes where possible, user-scoped tokens over org tokens, rotate quarterly, and never install an MCP server I haven't at least skimmed the source of.

## Where MCP is heading

The protocol is maturing from "neat demo" toward "plumbing." Recent public roadmaps from the maintainers have pointed at transport improvements, better agent-to-agent communication primitives, and enterprise-readiness features (audit trails, SSO auth, gateway patterns). MCP is turning into infrastructure; treat it accordingly in your architecture decisions.

## Source

`modelcontextprotocol.io` is the canonical home. The spec, maintainer roadmap, and server/client registries all live there.
