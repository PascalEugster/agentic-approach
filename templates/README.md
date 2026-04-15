# Templates

Starter artifacts for cold-starting a new project or a new phase. Empty for now. Planned:

## Planned

- **`.claude/` starter directory.** Complete starter kit: `settings.json` with my baseline hooks, an opinionated `CLAUDE.md`, topic rule files (auth, database, UI, testing, deployment, server layer), a small set of useful skills, a code-review sub-agent, and a pointer to the default plugins.
- **`AGENTS.md` template.** The monorepo-friendly variant for projects where every package needs its own context file.
- **`PROJECT.md` template.** The GSD project file format, pre-filled with the sections I always end up needing: product, constraints, stack, conventions, open questions, glossary.
- **Hook script library.** Reusable `pre-bash-safety.sh`, `pre-bash-typecheck.sh`, `post-edit-lint.sh`, `worktree-setup.sh`, with documentation of each.
- **QRSPI phase directory template.** `.planning/phases/NN-feature-name/` with DISCUSS, RESEARCH, STRUCTURE, PLAN, VERIFICATION files pre-stubbed.

## The rule

> *A good template captures decisions, not code. If the template needs to explain itself, it should explain why the decisions are what they are — not how to write the code the template already contains.*
