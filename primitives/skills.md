# Skills

> If I've explained it twice, it's a skill.

A skill is a reusable capability the agent can invoke by name. It's a markdown file with frontmatter describing *when to use it* and a body describing *how*. Optionally it can bundle helper scripts, templates, or supporting files.

## The SKILL.md shape

Claude Code uses the open Agent Skills standard. Each skill lives in a directory with a `SKILL.md` as the entry point:

```
.claude/skills/
└── my-skill/
    ├── SKILL.md           # metadata + body
    ├── helpers/           # optional scripts
    └── templates/         # optional file templates
```

Frontmatter is the contract:

```markdown
---
name: my-skill
description: When to use this skill (one line — this is what the agent matches against)
---

# My Skill

Body — steps, examples, templates, anything the skill needs to do its job.
```

## Progressive disclosure

Skills load in stages — this is the performance trick:

1. **Metadata** — always in the agent's context. Name + description. The "index card" the agent uses to decide whether the skill is relevant. Short, ideally ~1 sentence.
2. **Body** — loaded on demand when the skill is invoked. The full markdown.
3. **Supporting files** — loaded only when the body references them.

This is why you can have many skills available without blowing up the context window. Only the index cards are always present; the full content only appears when needed. Check the Claude Code docs for the current format details — exact field names and file layout conventions have shifted over time.

## What I actually build skills for

- **`deploy`** — project-specific deploy ritual. Env checks, migration push, build, verify.
- **`db-migrate`** — migration authoring, linking, pushing, verification.
- **`dev-logs`** — tail the right logs in the right format for the current stack.
- **`ci-status`** — read CI state, interpret failures, suggest fixes.
- **`playwright-cli`** / **`playwright-e2e`** — run browser automation for QA or headless scripting.
- **`service-builder`** / **`server-action-builder`** / **`react-form-builder`** — scaffolding skills for repetitive structural code.
- **`postgres-expert`** — database review and query tuning.

## The two failure modes

1. **Overly specific skill** — describes one past task, not a reusable shape. Delete it, write it as a command or a memory instead.
2. **Skill that should be a hook** — "after every edit, run the formatter" is a hook, not a skill. Skills are invoked; hooks fire automatically.

## Built-in skills worth knowing

Claude Code ships (or has plugins for) a range of generally useful skills — code simplification reviewers, security reviewers, debuggers, and so on. Before building your own skill, browse the public plugin marketplaces and the `anthropics/skills` GitHub repo — a lot of good ones already exist and aren't worth reinventing.

## Promotion path

Project skill → used on a second project → promote to user level (`~/.claude/skills/`) → used by a teammate → package as a plugin in a marketplace repo.

## Source

Claude Code's official documentation is the canonical place for the current skill format, built-in skills, and plugin marketplace pointers.
