# Benchmarks & ROI

Honest framing on what agents do and don't buy you. I keep this section deliberately sober — public discourse about AI productivity tends to run well ahead of the rigorous research.

## Index

- [**Coding benchmarks**](coding-benchmarks.md) — SWE-bench, Terminal-Bench, what the scoreboards actually measure.
- [**Productivity paradox**](productivity-paradox.md) — The gap between self-reported and measured gains.
- [**Quality & security**](quality-and-security.md) — The cost side. Defect rates, review overhead, attack surface.

## Three theses I keep coming back to

### 1. Scaffolding matters as much as the model

Running the same model through different agent frameworks can produce meaningfully different results on the same benchmark set. Harness quality is not a detail — it's roughly half the result. This is why my time spent on hooks, skills, and rules compounds more than my time spent chasing the latest model.

### 2. Honest organizational ROI is modest, not an order of magnitude

Rigorous studies (in particular METR's randomized-trial work on experienced developers) have consistently found that **self-reported productivity gains significantly overstate measured gains**. The direction is robust across repeat studies; the exact magnitude is still being debated.

Vendor surveys and case studies tell a rosier story. Both can be true: individual task-level speedups are real, but Amdahl's Law applies — coding is only a portion of the software development lifecycle. An organization that makes the *coding part* twice as fast doesn't necessarily ship twice as much, because all the other work (design, review, debugging, deploying, supporting) doesn't automatically speed up along with it.

### 3. The quality tradeoff is real

Multiple independent analyses have found that AI-generated code has measurably *more* defects and *more* security vulnerabilities than comparable human-written code. The exact multipliers vary between sources, but the direction is consistent across vendor research (CodeRabbit, Veracode, Snyk, Black Duck) and academic work.

This doesn't mean "don't use agents." It means: **review them like productive but unreliable contributors whose code happens to compile**. Martin Fowler has written compellingly about this posture — treating every AI-generated diff as "a PR from a rather dodgy collaborator who's very productive in the lines-of-code sense, but whom you can't trust without review."

## The 80% problem

Addy Osmani (Google) has described the dominant failure mode concisely: agents generate the first 80% of a solution fast, but the remaining 20% creates *hidden, compounding* costs. "Almost-right-but-not-quite" AI output is the most common frustration cited in practitioner surveys.

The patterns that help are all about making that last 20% cheaper to catch and fix: strict verification gates ([Planner/Generator/Evaluator](../patterns/planner-generator-evaluator.md)), forced structure steps ([QRSPI](../patterns/qrspi.md)), mandatory human review at a submission boundary ([Stripe Minions](../case-studies/stripe-minions.md)). None of these speed up generation; they speed up *correction*, which is where the real delivery cost lives.

## My reading of the numbers

For the kind of work I spend most of my time on — new features in familiar frameworks, component scaffolding, test authoring, bug triage, migrations — I see real, reproducible speedups. Not 10×. A meaningful multiple, contingent on all of the following being true:

- I actively review everything the agent produces.
- I invest significantly in the harness layer so the agent rarely generates junk in the first place.
- I measure by *shipped features*, not by *lines of code*.
- I'm disciplined about not counting "time saved" that I immediately spend on review or rework.

If I were running a team and setting expectations, I'd budget modest organizational gains in year one, growing as the harness matures. Anything more aggressive is setting people up to be disappointed.
