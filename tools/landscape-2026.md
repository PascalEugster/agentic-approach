# The competitive landscape

A snapshot of the coding-agent market. I maintain this so I know what the alternatives can do — not so I keep switching. Subject to heavy drift; treat it as directional, not authoritative. Check dates before trusting any specific number, and **always verify the current state from the vendor directly** before making a decision.

## The main players

### Cursor (Anysphere)

An IDE-first coding agent. Strong polish, strong team-rollout story, good IDE ergonomics. Widely adopted in larger engineering organizations that want a team-wide tool with minimal per-developer setup. Less composable at the harness level than Claude Code — fewer hook/skill/primitive surfaces to build on — but for people whose primary interface is the IDE, the experience is hard to beat.

**When I'd pick it:** the IDE is your primary tool, you want team-wide rollout with minimal setup, you value polish over configurability.

### OpenAI Codex

OpenAI's terminal/IDE coding agent offering. Strong on autonomy and parallelism. Generally where I'd look first for OpenAI-first shops.

**When I'd pick it:** you're already on OpenAI models, your stack is built around OpenAI infrastructure, and you want agentic capabilities without adopting a second vendor.

### Devin (Cognition AI)

Positioned as "autonomous software engineer" — the strongest "throw it a ticket, come back to a PR" story in the market. Good Slack / issue-tracker integration. Less interactive than Claude Code or Cursor. Cognition has made acquisitions in the IDE/agent space (previously-independent tools have been absorbed into their product line), so expect their surface area to keep evolving.

**When I'd pick it:** ticket-driven workflows where the goal is "hand off and come back later" rather than "pair with me interactively."

### Google Jules / Gemini-powered agents

Google's push into coding agents, powered by the Gemini family. Strong inside the Google ecosystem. Outside GCP, less compelling than the Claude or OpenAI options — but the integration with Google Cloud and Workspace is real.

**When I'd pick it:** deep Google ecosystem tie-in, GCP-heavy stack.

### GitHub Copilot Agent Mode

GitHub's native agentic mode. Strong if you're deeply embedded in GitHub's issue/PR/Actions workflow. Tight integration with code review, Actions, and repository-level automation.

**When I'd pick it:** GitHub-native org, minimal-setup preference, the tight PR/issue integration is your killer feature.

## Rough segmentation

| Use case | Pick |
|----------|------|
| Deep IDE integration, team rollout, polish | Cursor |
| OpenAI-first shop, parallel agents, autonomy | Codex |
| Ticket-to-PR autonomy | Devin |
| Google / GCP ecosystem | Jules |
| GitHub-native workflow, PR automation | Copilot Agent Mode |
| Composable harness with skills / hooks / plugins / MCP | **Claude Code** |

## The caveat

The gap between these tools has collapsed faster than I expected. A year or two ago they were meaningfully different in *what they could do*. Today they differ more in *how they make you work* than in *whether they can do the work at all*. Pick the one whose workflow shape matches yours — not the one with the best demo video.

## Numbers I'm deliberately not publishing

I used to keep ARR, valuation, and market-share numbers in this file. I've stripped them. Private-company financials from third-party reporting are a reputational trap: the numbers are usually leaked or estimated, they age in weeks, and if you quote them in a public talk and they turn out to be wrong, the person you embarrass is yourself.

If you need current numbers for a pitch deck or a strategy conversation, pull them from the latest credible source at the moment you need them — and cite the source explicitly.

## Where to watch for changes

- Each vendor's official changelog and blog.
- Benchmark-focused coverage (SWE-bench, Terminal-Bench, and similar evaluations that actually test coding tasks).
- The Latent Space podcast and similar AI engineering outlets for credible independent takes.
- Your own workflow. The only benchmark that matters for *you* is how each tool performs on the work you actually do.
