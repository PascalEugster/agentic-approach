# Reading list

Writing and talks I've found useful, grouped by how often I come back to them. I've deliberately **not** included deep-linked URLs — the field moves too fast for links to stay stable, and I'd rather you search for the latest version of a piece than follow a stale URL. Author and title are enough.

> **Note on sources.** I haven't verified every attribution below to the letter. Treat this list as "starting points for your own research," not as a fact-checked bibliography. When something looks interesting, search the author's blog or YouTube directly.

## Foundational — start here

- **Simon Willison** — ongoing writing on agentic coding in practice at `simonwillison.net`. The single best continuously-updated source on what LLM-assisted coding actually looks like day to day. Notes, experiments, failures, patterns.
- **Anthropic Engineering** — their own engineering blog on how they build and use Claude Code internally. Search for posts on harness design, long-running agents, context management.
- **Martin Fowler & Birgitta Böckeler** — Thoughtworks' ongoing writing on AI-assisted engineering. Their "genuine AI" framing and skepticism of AI slop are both worth internalizing.
- **Dex Horthy / HumanLayer** — talks and writing on context engineering and phase-based agent work. The structural vocabulary for how research, planning, and execution should be separated.

## Context engineering

- **Andrej Karpathy** — the "LLMs as a new OS" framing, `autoresearch`, and his ongoing commentary on where the field is heading.
- **Drew Breunig** — the taxonomy of context failures: Poisoning, Distraction, Confusion, Clash. A useful diagnostic vocabulary when an agent session is going wrong.
- **Research on position effects in long contexts** — the "lost in the middle" phenomenon is well-documented in recent NLP literature. Worth knowing the mechanism even if you never cite the paper.

## Management and strategy

- **Ethan Mollick** at `oneusefulthing.org` — the best translator of AI capability into business implications. Wharton professor, readable, calibrated. The one I send to non-technical stakeholders.
- **Tobi Lütke** (Shopify CEO) — ongoing writing on X about operating a company at high AI adoption. His public experiments are concrete and worth studying.

## Production case studies

- **Spotify Engineering blog** — their public writing on scaling AI-assisted coding across a large codebase. The most detailed public case study I know of at the time of writing.
- **The Pragmatic Engineer** — Gergely Orosz's deep dives on how real companies are adopting AI coding tools. Subscription newsletter, worth every cent.
- **Latent Space** (Shawn "swyx" Wang) — the most important AI engineering podcast and conference. Good taste in guests, high signal.

## Productivity and honest numbers

- **METR** — their rigorous RCT work on AI productivity in real engineering contexts. Search their blog for the most recent findings; this is the one study I'd read first if I only had time for one.
- **DORA reports** — delivery metrics across AI-adopting teams. A reality check on "we got 10× faster" claims.

## Security and quality

- **OWASP Top 10 for LLM Applications** — the baseline threat model for anything that ships an LLM to users.
- **Martin Fowler** on "AI Slop" — the "dodgy collaborator" framing, useful for calibrating review posture.
- **Black Duck / Synopsys OSSRA reports** — annual open-source security reports, worth scanning for YoY trends.

## Benchmarks

- **SWE-bench** and its variants — the Princeton/Stanford benchmark suite for coding agents. Useful but known to be partially contaminated; read the papers critically.
- **Terminal-Bench** — closer to real-world agentic terminal tasks than SWE-bench. Worth watching scores.

## Podcasts worth following

- **Latent Space** — technical depth, builder focus.
- **No Priors** (Sarah Guo, Elad Gil) — strategy-level conversations.
- **The Pragmatic Engineer Podcast** — case studies and practice.

## The rule

> *Don't read everything. Read the foundational list carefully, skim the rest monthly, and come back to the case studies whenever you find yourself arguing about whether something "really works in production."*

Every time I've optimized for breadth over depth, I've produced less.
