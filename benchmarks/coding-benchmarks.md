# Coding benchmarks

A quick reference for what the common coding-agent scoreboards measure and how much to trust them. **These numbers age in weeks, not months — I am deliberately not listing current leader scores.** Check the benchmark's own leaderboard when you need a current answer.

## SWE-bench Verified

A curated set of real GitHub issues, widely treated as the industry standard for coding-agent capability. Has been the benchmark model providers chase for over a year now — which has become its problem.

**Caveats worth knowing:**

- **Partial contamination.** Many of the issues have been seen by training data at least indirectly.
- **Scaffolding sensitivity.** The same model in different frameworks produces meaningfully different scores. The leaderboard number is "model plus the scaffold that was tuned for the benchmark," not "this model is that good."
- **Python-only.** Real-world coding is usually multi-language; SWE-bench Verified isn't.

## SWE-bench Pro

A larger, multi-language, contamination-resistant follow-up. Harder and more honest than Verified. Scores on Pro are usually significantly lower than on Verified for the same model — that gap is the interesting data point, not the absolute number.

## Terminal-Bench

Agentic terminal tasks — closer to "can the agent actually run a dev workflow" than to "can it produce a diff." Probably the most useful of the common benchmarks for predicting real-world coding-agent utility, because it tests the full loop of figure-out-the-task / run-things / interpret-output / make-progress.

## The scaffolding caveat

Multiple analyses have shown that running the *same model* through three different agent frameworks can produce substantially different scores on the same benchmark set. The takeaway: **scaffolding matters roughly as much as the model.** This is why benchmark comparisons between models are only half the story, and why most of my own agent-improvement work goes into hooks, skills, and rules — not into chasing the latest model.

## What I actually watch

- **Terminal-Bench** as a rough proxy for end-to-end agent capability.
- **SWE-bench Pro** as the contamination-resistant sanity check.
- **Scaffolding sensitivity studies** — whenever a new model launches, the headline number is 80% marketing and 20% signal.
- **My own workflow results.** The only benchmark that matters for my day-to-day is "did this model ship better code on the kind of tasks I actually do," which no public benchmark can answer.

## The rule

> *Benchmark scores tell you which models can do the thing under ideal conditions. They tell you almost nothing about which model will do the best job on your actual workflow.*

For "should I switch from model A to model B on the kind of work I do," the answer isn't on any leaderboard — it's in honest side-by-side testing on tasks from your own backlog.

## Where to find current numbers

- **SWE-bench / SWE-bench Pro** — the benchmark organizers publish and update leaderboards.
- **Terminal-Bench** — the Stanford / Laude Institute group maintains its own leaderboard.
- **METR** — for productivity-level measurements that go beyond coding-task benchmarks.
- **Independent vendor-comparison writeups** from sources you trust. Beware of vendor-published benchmarks; they're always flattering.
