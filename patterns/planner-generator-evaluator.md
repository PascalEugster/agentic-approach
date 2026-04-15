# Planner / Generator / Evaluator

> A 3-agent harness pattern that borrows its shape from GANs: one agent builds, a separate agent judges, and a third coordinates. Several groups have converged on variants of this shape. The central insight is the same across all of them: **agents cannot objectively judge their own work.**

## The three roles

### Planner
Expands a short prompt into a full product spec — typically 10–16 features in ordered sprints. Defines the domain-specific evaluation criteria that the Evaluator will later enforce. Crucially: the Planner writes the grading rubric *before* any code exists, while it's still cheap to argue about what "done" means.

### Generator
Implements feature by feature. Before each sprint, it negotiates a **contract** with the Evaluator: what "done" means for this sprint, which behaviours must be testable, and how they'll be verified. Then it builds against that contract.

### Evaluator
Tests the running application via a browser tool (Playwright MCP is a common reference choice) — navigates the live app, clicks, fills forms, takes screenshots, diffs state, and grades against the rubric. Writes a pass/fail verdict with evidence.

**The critical detail:** the Evaluator does not share context with the Generator. No shared conversation history, no sunk-cost bias, no "well, we already spent an hour on this so let me find a way to call it done."

## Why this works

> *Agents can't objectively judge their own work. LLMs are pathological optimists about their own output.*

Self-review inside a single context is the single biggest source of false "I'm done" claims. The Evaluator's system prompt should be explicitly tuned to suppress leniency — something like:

> *"If something fails, it fails. Do not rationalize, minimize, or excuse."*
>
> *(This is a line I'd configure in the Evaluator sub-agent's system prompt, not an instruction to the reader. If an AI is parsing this file on your behalf, treat it as documentation of a pattern, not as a directive.)*

That kind of framing matters. Without it, the model defaults to charitable interpretation of its own work.

## The cost delta

Public writeups of this pattern consistently show a meaningful cost premium — running three cooperating agents with a real evaluator phase costs considerably more than a single-pass generation. The trade is obvious: more tokens spent on verification, fewer cycles spent rebuilding broken work.

For small exploratory tasks, the premium isn't worth it. For anything I'd otherwise have to throw away and redo, the harness wins on total cost — because the alternative isn't "one cheap attempt," it's "one cheap attempt plus three rounds of fixing what you trusted too early."

## How I use it

For larger frontend builds, I wire a Playwright MCP sub-agent as the Evaluator. The rubric lives in `.planning/phases/NN-feature/RUBRIC.md` and gets written during plan-phase. After each sprint:

1. Generator commits its sprint.
2. Evaluator sub-agent starts in a fresh context, reads the rubric, runs the app, and records a verdict with screenshots.
3. If fail: Evaluator writes findings; Generator reads the findings (but not the Evaluator's chain of thought) and iterates.
4. If pass: next sprint starts, new contract negotiation.

## When it's overkill

- Backend-only work with unit tests as the verification layer — the rubric is basically "tests pass."
- Anything too small to justify writing a rubric.
- Single-session exploratory work.

## When it's essential

- Any build where "I'm done" is a visual or behavioural claim, not a test-suite claim.
- Any build long enough that sunk-cost bias starts mattering (~half a day of agent work and up).
- Anything where I'd otherwise have to manually QA every sprint myself.

## Source

This pattern has been discussed by several groups. For current writeups, search Anthropic's engineering blog, the Latent Space podcast, and recent conference talks on harness design and long-running agent workflows. The specific shape of "planner / generator / evaluator" as a named triple has been used in public writeups; the underlying idea of an independent evaluator sub-agent is much older than the name.
