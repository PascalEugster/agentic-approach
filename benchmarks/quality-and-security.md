# Quality & security cost

The other side of the productivity question. Agent-generated code is faster to produce and, in multiple independent analyses, *measurably lower-quality* across several dimensions. Not catastrophic — but enough to matter, and the gap compounds when review isn't catching it.

## The pattern across public research

Several independent analyses have looked at quality and security of AI-generated code vs. human-written equivalents. The details vary and I don't want to quote specific multipliers I can't source to the letter — but the *direction* of the findings is consistent:

- **More logic errors per unit of code** in AI-generated output than in comparable human-written code.
- **More vulnerabilities that map to the OWASP Top 10**, particularly XSS, injection, and authorization bypasses.
- **Year-over-year growth in per-codebase vulnerabilities** in broader software ecosystem reports, correlating with (though not cleanly attributable to) rising AI-assisted code generation.
- **A "sustainable" AI-code ratio somewhere below ~40%**, above which rework and review times start rising sharply.

If you want specific numbers for a talk or a post, go to the original sources (Black Duck / Synopsys OSSRA annual reports, vendor research like CodeRabbit or Veracode writeups, academic work on LLM code security). Treat vendor-published numbers with appropriate skepticism — they all have incentives — but the qualitative story is robust across sources.

## The review overhead trap

The same research that finds higher PR volume from AI-adopting teams also finds significantly longer review times. Reviewers are looking at more code per unit time, and the code is lower-quality on average. The rational response is to review more carefully, which slows delivery back down. The irrational response is to review less carefully, which shows up weeks later as production incidents.

This is the central tension I keep coming back to: **generation is cheap, review is expensive, and the ratio has shifted in the wrong direction.**

## Attack surfaces an agent setup introduces

Every agent setup adds new attack surfaces worth thinking about explicitly:

- **MCP servers with write access to external systems.** Compromise one, and the agent can be used to exfiltrate data or impersonate users.
- **Prompt injection via tool output.** Anything an agent reads is untrusted input. A malicious README, an adversarial issue comment, a hostile search result — any of these can attempt to redirect the agent.
- **Credential scope sprawl.** Tokens accumulate across services; rotation becomes a coordination problem.
- **Memory poisoning.** Persistent memory files can be written with misleading "facts" that shape future sessions.
- **Tool confusion.** Too many available tools → the agent picks the wrong one → unintended writes.

None of these are hypothetical. They're all documented in the wild.

## The mitigation set I actually use

- **Hooks for destructive commands.** Block `rm -rf` patterns, force-push to protected branches, direct DB drops, and similar. Non-negotiable.
- **Narrow MCP scopes.** Read-only where possible, user-scoped tokens over org tokens, rotate regularly.
- **No auto-push to production branches.** Submission authority, not merge authority. Everything goes through human review.
- **Mandatory typecheck + lint + test gates.** The agent cannot skip them — hooks enforce it.
- **Security review sub-agent on every feature phase.** Looks specifically for injection, secrets, authorization bypasses, unsafe defaults.
- **Periodic sanity audits of memory files.** Stale or surprising entries get deleted.
- **No secrets in CLAUDE.md, rules, memory, or prompts.** Secrets live in `.env`, never in context.

## The framing I keep coming back to

Martin Fowler has written compellingly about treating AI-generated code with the same skepticism you'd apply to a productive but unreliable collaborator — one whose output you can't trust without review. That framing has calibrated my review posture better than any tool setting. The agent is capable, productive, and deeply unreliable. The harness exists to make that unreliability safe to operate at scale.

## The throughput vs. reliability trade

Every time I've had a bad incident downstream of an agent-written change, the root cause was a review I should have done more carefully. The agent produced plausible-looking code; I skimmed it; I missed something subtle; the pipeline caught it later.

The fix isn't "use a better agent." The fix is "build review checkpoints into the harness so the things I would have missed get flagged before I see the diff." That's what the [Planner/Generator/Evaluator pattern](../patterns/planner-generator-evaluator.md) is really about, and why the [Stripe Minions](../case-studies/stripe-minions.md) submission-only authority boundary matters so much.

## The rule

> *Speed is only a win if it doesn't increase the rate at which bad code reaches production. Any productivity claim that doesn't account for incident rate is marketing.*

## Where to read more

- **Black Duck / Synopsys OSSRA annual reports** on open-source vulnerability trends.
- **Vendor security research** (CodeRabbit, Veracode, and similar — filter for incentive bias).
- **Martin Fowler's writing** on AI-assisted coding at `martinfowler.com`.
- **OWASP Top 10 for LLM Applications** — the baseline threat model for any LLM-integrated system.
