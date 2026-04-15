# Spotify

> One of the most detailed public discussions of agentic coding inside a large engineering organization. Spotify's engineering team has published a multi-part blog series and given conference talks on their AI-assisted workflow.

> **A note on specifics.** Public reporting has included striking numbers — merge counts per month, percentage time savings on migrations, comments from leadership about engineer time shifting from writing code to reviewing agent output. Exact figures vary between sources, and some of them have been amplified in secondhand coverage. Rather than repeat numbers I can't source to the letter, I'll focus on the *patterns* Spotify has been transparent about. If you want the headline stats for your own talk or blog post, **go to the original Spotify Engineering blog posts and talks directly**.

## The patterns Spotify has been clear about

### 1. Per-repository context, not universal rules

Spotify hasn't tried to write a single global CLAUDE.md or universal ruleset. Instead, they've invested in **per-repository context packs** — each one curated to match the conventions of that specific codebase.

This matters because a universal ruleset doesn't scale to thousands of internal repos with different architectures, languages, and conventions. Curating context per repo is expensive up front and cheap at run time. The inverse — cheap up front, expensive at run time — is what most teams accidentally do, and it's why their agent experiences plateau.

### 2. Context engineering as the main investment

The consistent message from Spotify's public writing is that the agent itself (Claude Code, off the shelf) is not where they've spent the effort. The effort went into the *wrapper around it*: prefetching the right context, curating the right tools, enforcing the right conventions, feeding the model the right slice of the codebase for each task.

If there's one thing to take from Spotify's reporting, it's this: **the model is a commodity, the context layer is not.**

### 3. Migrations as the highest-leverage use case

Migrations (language versions, framework upgrades, dependency bumps, security remediations) show up repeatedly in Spotify's public discussion as the place where AI-assisted workflows pay off most. This matches what I see in my own work — mechanical, well-specified tasks at scale are where agents shine; ambiguous architectural work is where they struggle.

### 4. Human review as a non-negotiable checkpoint

Every public description of Spotify's workflow keeps the human code-review step. Agents propose; humans approve. This is the same submission-authority pattern Stripe uses for its Minions.

## What's transferable to a small team

- **Invest in per-project context before scaling agent usage.** A thoughtful `CLAUDE.md` + rule files + skills will deliver more than upgrading the model.
- **Pick migrations and mechanical refactors as your first targets.** Don't start with the hardest architectural task.
- **Measure by merged PRs (or shipped features), not by generated lines.** Lines of code is a vanity metric that correlates poorly with value.
- **Keep human review in the loop.** Submission authority, not merge authority.

## What isn't transferable

- **Full-time context engineers.** A small team can't dedicate headcount to context tuning the way Spotify can. The workaround is to do it incrementally during regular work.
- **An internal tool fleet.** I can't build Spotify's internal tooling; the substitute is a curated set of public MCP servers.

## The lesson I extracted

The agent is a commodity. The *context packs around it* are not. Spend time curating rules, skills, and project context — spend less time prompt-tuning or model-shopping.

## Where to go for details

- **Spotify Engineering blog** — primary source for patterns and case studies.
- **QCon and similar engineering conference talks** — where Spotify has walked through their approach in more detail.
- **The Pragmatic Engineer** has also done coverage of Spotify's adoption of AI coding tools that's worth reading.
