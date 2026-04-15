# Hooks

> If it must happen every time, it's a hook. Not a prompt, not a rule, not a hope.

A hook is a shell command that the Claude Code harness runs at specific lifecycle events. Hooks are deterministic — they run regardless of which model is driving, regardless of whether the agent "remembered" to do the thing.

## Lifecycle events

Claude Code exposes 10+ events. The ones I use:

| Event | When it fires | What I use it for |
|-------|---------------|-------------------|
| `SessionStart` | Beginning of a session | Inject project context, check for updates |
| `UserPromptSubmit` | User sends a prompt | Auto-suggest relevant skills, inject domain chunks |
| `PreToolUse` | Before a tool call | Block dangerous commands, enforce invariants |
| `PostToolUse` | After a tool call | Lint, format, validate |
| `SubagentStop` | Sub-agent finishes | Record results, update state |
| `Stop` | Turn ends | Notifications, handoff prep |
| `WorktreeCreate` | New worktree created | Copy env files, install deps, configure hooks |

## My baseline three

Every project I set up gets at least these:

### 1. `PreToolUse / Bash` — safety

```json
{
  "matcher": "Bash",
  "hooks": [
    { "type": "command", "command": "./scripts/hooks/pre-bash-safety.sh", "timeout": 5000 }
  ]
}
```

Blocks `rm -rf /`, force-push to `main`, `DROP TABLE` without explicit confirm, `git reset --hard` on an unpushed branch, etc. Uses a shell allowlist/denylist pattern. Non-negotiable.

### 2. `PreToolUse / Bash` — typecheck gate

```json
{
  "matcher": "Bash",
  "hooks": [
    { "type": "command", "command": "./scripts/hooks/pre-bash-typecheck.sh", "timeout": 180000 }
  ]
}
```

Before any "run the app" or "ship" command, run the typechecker. If it fails, the Bash call is blocked until the types are clean. Catches roughly half the "I thought I was done" failures I used to see.

### 3. `PostToolUse / Edit|Write` — lint + format

```json
{
  "matcher": "Edit|Write",
  "hooks": [
    { "type": "command", "command": "./scripts/hooks/post-edit-lint.sh", "timeout": 15000 }
  ]
}
```

Every file the agent writes goes through the formatter immediately. Tiny feedback loop, keeps diffs clean.

## Hook anatomy

A hook script reads JSON from stdin (the tool call payload), does its work, and either exits 0 (allow) or prints a block reason and exits non-zero. Exit codes and JSON output are how the hook communicates back to the harness.

```bash
#!/usr/bin/env bash
set -euo pipefail
payload="$(cat)"
command="$(echo "$payload" | jq -r '.tool_input.command')"

if echo "$command" | grep -qE '(rm -rf /|git push.*--force.*main)'; then
  echo "Blocked: destructive command" >&2
  exit 2
fi
exit 0
```

## The invariant rule

> *Hooks should enforce invariants, not opinions.*

If it's an opinion ("I prefer tabs over spaces"), write a rule or a CLAUDE.md line. If it's an invariant ("no unformatted code ever gets committed"), write a hook. The test: if a violation of the thing would be a bug, it's an invariant. If a violation would be a style choice, it's an opinion.

## When hooks misfire

- **Too slow.** A 30-second hook on every Edit makes the session unusable. Cap tight timeouts.
- **Too noisy.** A hook that prints on every tool call trains the agent to ignore its output. Stay silent on success.
- **Too strict.** Blocking too aggressively means the agent can't do legitimate work. Fail loud, fail specific.

## Source

Claude Code hooks docs: code.claude.com/docs/en/hooks. Hookify plugin (included in the Claude plugin marketplace) for authoring hooks from conversation analysis.
