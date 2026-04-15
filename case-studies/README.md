# Case studies

Public case studies I've found useful for calibrating my own expectations. Each one gets its own file with the "what they built, how they built it, what's transferable" shape.

## Index

- [**Spotify**](spotify-honk.md) — Publicly discussed AI-assisted coding at scale. Patterns worth copying; exact stats change across sources, so go to the primary reporting for numbers.
- [**Stripe — Minions**](stripe-minions.md) — Agentic PR-production at high throughput. The source of the [Blueprint pattern](../patterns/blueprint-pattern.md).
- [**Others**](others.md) — Shopify, Anthropic, TELUS, Zapier, Rakuten. Shorter summaries of publicly discussed adoption.

## The meta-observation

The organizations reporting the biggest gains all had mature engineering infrastructure *before* adopting agents. Good tests, good CI, clean dependencies, reliable deploys. The agent is a multiplier on that foundation.

The organizations reporting mixed results usually had one or more of: flaky tests, inconsistent CI, tangled dependency graphs, slow review culture. The agent doesn't fix any of those — it amplifies them.

> *The precondition for agentic engineering at scale is the engineering hygiene you should already have.*

This is the single most important thing I've taken from reading the case studies.
