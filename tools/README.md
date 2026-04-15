# Tools

The landscape of agent harnesses and frameworks as of early 2026. I don't use most of these — but knowing what exists helps me explain *why* I picked what I picked.

## Index

- [**Claude Code**](claude-code.md) — The harness I actually use. Skills, hooks, sub-agents, commands, plugins, MCP.
- [**The competitive landscape**](landscape-2026.md) — Cursor, Codex, Devin, Jules, GitHub Copilot Agent Mode. The main alternatives to Claude Code.
- [**Agent frameworks**](frameworks.md) — LangGraph, OpenAI Agents SDK, Claude Agent SDK, Google ADK, CrewAI, n8n, plus adjacent areas like durable execution and agent evaluation. The libraries for building agents yourself.

## My stack at a glance

| Layer | Tool | Why |
|-------|------|-----|
| Coding harness | Claude Code | Composable primitives (skills, hooks, sub-agents, commands), strong plugin ecosystem, first-class MCP. |
| Models | Opus 4.6 (plan), Sonnet 4.6 (execute), Haiku 4.5 (read-heavy) | Cost/quality tiering based on task shape. |
| Workflow | GSD plugin | Phase-based, artifact-driven, matches QRSPI + P/G/E thinking. |
| Orchestration | n8n (when needed) | Self-hostable, EU-friendly, MCP support, 400+ integrations. |
| External systems | MCP servers | Linear, Notion, Figma, Next.js devtools, Playwright, Supabase, Vercel, Context7, Firecrawl. |
| Default app stack | Next.js 16 + Supabase + Vercel | My baseline for SaaS work. Agent tooling is dense around this stack. |

## The principle

> *Pick one harness and learn it deeply. The leverage comes from the primitives, not from having five tools.*

I tried the "use every agent" phase. It's a trap. Every harness has its own conventions, its own skills, its own hook model, its own failure modes. Split attention means shallow setup everywhere.

Better: one harness, deep setup, and an honest understanding of what the competitors do better than yours — so I know when to switch tactics without having to switch tools.
