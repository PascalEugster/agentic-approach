# Context Engineering

> *"LLMs are a new OS. The LLM is the CPU and the context window is RAM."* — Andrej Karpathy

Over the last year or so, "context engineering" has largely displaced "prompt engineering" as the central craft in building with LLMs. The framing has been championed by Tobi Lütke (Shopify), Andrej Karpathy, and Dex Horthy (HumanLayer) among others, and has been formalized in writing from multiple tool vendors and IDE teams. The thesis: the quality of your output is mostly a function of *what you put into the context window*, not *how cleverly you phrase it*.

## Why it matters

- **Lost-in-the-middle.** Stanford/UC Berkeley research shows correctness loss starting around ~32,000 tokens, even on models with much larger context windows. More context ≠ better output. Ordering matters.
- **Context poisoning.** Once a hallucination lands in the context, the agent builds on top of it. Hard to undo.
- **Cost.** Tokens are money and latency. Unused context is waste in both.
- **Steering.** A bloated main session becomes harder to redirect. The model takes longer to "notice" a course correction.

## Five strategies

### 1. Selection

Pull in only relevant context. Combine grep, file search, embedding search, and re-ranking. Prefer narrow slices of files over whole files.

> *Default: read the one function you need, not the file. Default: grep the repo, don't ingest it.*

### 2. Compression

Remove tokens that don't change the output. Techniques range from aggressive summarization to research-grade prompt-compression tools (Microsoft has published work on this under the "LLMLingua" family, for example). For long conversations, compact prior turns once they stop being load-bearing.

### 3. Ordering

Hierarchical layouts signal importance. Put critical instructions near the top and near the bottom; the middle is the weakest position. Structure beats prose — tables and lists outperform paragraphs for retrieval.

### 4. Isolation

Use sub-agents for anything wide-reading or noisy. The main context becomes a scheduler; sub-agents handle file search, code analysis, test execution, research. The sub-agent's full trace never enters the main context — only the verdict does.

### 5. Format

Structure matters. `CLAUDE.md`, `AGENTS.md`, and topic-specific rule files act as durable context containers. Markdown tables and headings help the agent navigate. JSON is fine for tool results but wastes tokens for instructions.

## Anti-patterns (Drew Breunig's taxonomy)

| Anti-pattern | What it looks like | How to fix |
|--------------|---------------------|------------|
| **Context Poisoning** | A hallucination lands in context, subsequent steps build on it. | Fresh context for new phases. Verify facts before building on them. |
| **Context Distraction** | So much context the model gets pulled away from the task. | Shorter prompts. Move detail to rules, load on demand. |
| **Context Confusion** | Irrelevant context changes the answer. | Scope the context window to the current domain. Use sub-agents. |
| **Context Clash** | Two parts of context contradict. | De-dup. Prefer authoritative sources. Explicit "if X says Y, trust Y." |

## The shapes that work

- **Index card CLAUDE.md.** Stack, structure, commands, git flow. Nothing domain-specific.
- **Topic rules.** `authorization.md`, `ui-components.md`, `database.md`. Loaded on demand.
- **Artifacts on disk.** Research, plans, verification reports live in `.planning/`. Re-readable, not carried in context.
- **Progressive disclosure.** Skills use a 3-stage load: metadata always, body on invoke, supporting files on demand.
- **Sub-agent filtering.** Noisy operations happen in an isolated context; only the verdict comes back.

## The rule I've internalized

> *Every time an agent session starts to feel "off" — sluggish, missing the point, repeating itself — the first question is not "which model?" or "what prompt?" but "what's in the context window right now, and what shouldn't be?"*

Usually the answer is stale research, old tool output, or a rule that got loaded for a task that's long since finished.

## Source

- Andrej Karpathy's public writing on "LLMs as a new OS"
- Dex Horthy / HumanLayer — talks and posts on context engineering for agents
- Drew Breunig — *The Four Failures of Context*
- Anthropic's official context management documentation
- Recent writing from IDE / tooling vendors on context engineering — search directly; titles and URLs shift fast
