# Other case studies

Shorter writeups of organizations whose public discussion is worth knowing. Deliberately light on specific numbers — headline stats from secondhand reporting are a reputational hazard, and the patterns matter more than the percentages.

## Shopify

**What's public:** Shopify has been vocal about aggressive AI adoption across the engineering org. Leadership has framed it as a cultural expectation — teams are asked to demonstrate why AI-assisted workflows *can't* solve a problem before adding headcount to solve it. The VP of Engineering and the CEO have both written publicly about the transition.

**The notable experiment:** CEO Tobi Lütke has published his own hands-on experiments, including a widely-discussed run using Karpathy-style automated research workflows to optimize Shopify's Liquid template engine. The framing he shared on X is worth reading directly — it's one of the clearest public demonstrations of what a single motivated user with a strong harness setup can do in a short time.

**Transferable lesson:** the mental version of Shopify's mandate. Before I start a task by hand, *ask whether an agent-assisted version would be better*. Even if the answer is "no," you've forced yourself to think about it.

**Worth watching:** Shopify has published open-source tooling that bridges Claude Code / Cursor / other harnesses to live Shopify store data. A good reference if you're thinking about how to wire external system context into an agent setup.

## Anthropic (self-hosting)

**What's public:** Anthropic has been open about using Claude Code heavily to build Claude Code itself. The exact percentage varies across talks and interviews, and the number has probably kept moving. What's clear is that internal dogfooding is significant — maintainers of the harness are also its heaviest users.

**Transferable lesson:** the feedback loop. When the team building an agent trusts it enough to use it on the agent's own codebase, that's a signal about maturity. More importantly, it's a signal about *harness investment* — you don't trust an agent on complex code unless you've built the guardrails to make that trust safe.

**What I watch:** when Anthropic's engineering team describes a new internal pattern, that pattern usually shows up in Claude Code proper a few months later. The public talks and blog posts are a decent leading indicator for where the harness is heading.

## TELUS, Zapier, Rakuten

These show up in Anthropic's public case study material and in various conference talks as examples of enterprise-scale agentic coding adoption. I don't know their internal details well enough to write confidently about the architecture, but a few things are consistent across all three:

- **Many small agents, not one big one.** The successful orgs seem to build thousands of focused, specialized agents rather than one giant universal one. This maps to the skills-and-sub-agents philosophy — many small focused primitives beat one general-purpose primitive.
- **Adoption as a leading indicator, not a success metric.** High adoption is necessary but not sufficient. The orgs that get the most value pair high adoption with high review discipline.
- **Long-running autonomous sessions work — sometimes.** Public reporting includes cases where agents ran for hours with minimal supervision on large codebases. These cases exist and are real. They're also the upper tail — the median agent session is much shorter.

## Anthropic's enterprise case study material

Anthropic publishes its own write-ups on how customers use Claude. Treat these as vendor marketing (which they are) but also as useful data points — the patterns are usually accurate even when the percentages are selected for impact. Worth reading for the *architecture* and *workflow* details, less for the headline stats.

## The shared pattern

Every one of these case studies has the same shape underneath:

1. **Mature engineering infrastructure existed before the agents arrived.** Good CI, good tests, clean dependencies, disciplined ticketing. The agents multiply existing hygiene; they don't substitute for it.
2. **Significant investment in context engineering.** Per-repo or per-team context tuning. Not "one universal CLAUDE.md."
3. **Strong human review at a submission boundary.** Nobody has gone to merge authority. Everyone stops at propose.
4. **Specialized rather than universal agent setups.** Per-task, per-repo, per-role.
5. **Measured by shipped outcomes, not generated lines.**

Get those five right and the model you pick barely matters. Get them wrong and no model will save you.

## The meta-observation

There's a consistent finding across enterprise case studies: **the gap between top-performing individual users and average users is enormous — often estimated at an order of magnitude.** Same tools, same model, same company — but individual harness investment produces outsized differences in what gets shipped.

Whatever the exact multiplier, the message is the same: the model is a commodity, the harness is not, and individual investment in the harness compounds.

## Where to read more

- Anthropic's customer case study material (treat as vendor-curated but informative)
- Conference talks at QCon, KubeCon, Latent Space, and similar events
- Tobi Lütke's public writing on X and Shopify's engineering blog
