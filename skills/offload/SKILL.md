---
name: offload
description: Hand a self-contained coding task to a locally installed CLI agent (opencode, mimo, grok, codex) instead of doing it yourself, to save tokens on the primary model. Use for mechanical, well-specified, verifiable work such as bulk renames, repetitive edits, boilerplate, test scaffolds, or draft docs. Trigger when the user says "offload", "use the free agents", "save tokens on this", or when a subtask is grunt work with a cheap verification step.
---

# Offload

You have local CLI coding agents available as cheap labor. The `offload`
script runs a task on one of them headlessly and prints the agent's output.
Backends are tried in order (opencode, mimo, grok, codex by default) and a
backend that is missing or out of free-tier quota is skipped automatically.

## How to run it

```bash
"${CLAUDE_PLUGIN_ROOT}/bin/offload" --dir /path/to/repo "task prompt"
```

Options: `--backend NAME` to pin one backend, `--timeout SECS` (default 600),
`--list` to see what is installed. `OFFLOAD_BACKENDS` overrides the order.
The script exits nonzero when every backend failed.

## What to offload

Good candidates share three traits: the spec fits in one paragraph, the work
is mechanical, and you can verify the result cheaply (a test suite, a
compiler, a format gate, or a quick diff read).

- Repetitive multi-file edits: renames, import swaps, deleting a listed set
  of code blocks
- Boilerplate: fakes, fixtures, config stubs, CRUD scaffolds
- First drafts of tests for pure functions you name explicitly
- Mechanical format conversions (JSON to YAML, table to code)

## What to keep yourself

- Anything needing architectural judgment or cross-cutting design
- Delicate refactors where a subtle mistake passes the gates
- Security-sensitive code, auth, migrations, anything hard to reverse
- Tasks whose verification would cost more than doing the work

## How to prompt a backend

The backend has no context from your conversation. Write the prompt like a
ticket for a contractor who just cloned the repo:

- Name exact file paths and exact symbols
- State the acceptance criteria ("all call sites updated, `flutter analyze`
  clean")
- Say what NOT to touch, and tell it not to commit, push, or create files
  outside the repo
- One task per invocation; chain calls rather than batching unrelated work

## After it returns

Never trust, always verify. Read the diff (`git diff`), run the project's
gates, and fix or redo anything below your own bar. You own the result; the
backend is a typist, not a reviewer. If the output is unusable, do the task
yourself rather than iterating more than once with the backend.
