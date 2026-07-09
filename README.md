# offload

Cheap labor for expensive agents. `offload` lets a primary coding agent
(Claude Code, or anything that can run a shell command) hand self-contained
tasks to locally installed CLI agents: [opencode](https://opencode.ai),
[mimo](https://github.com/XiaomiMiMo), [Grok Build](https://grok.com), and
[Codex](https://github.com/openai/codex). The expensive model plans and
reviews; the free-tier agents type.

Backends are tried in order. One that is not installed, times out, or is out
of quota gets skipped, so free-tier limits degrade to the next agent instead
of failing the task.

## Install as a Claude Code plugin

```
/plugin marketplace add Project516/offload
/plugin install offload@offload
```

The plugin ships a skill that teaches the agent what to delegate (mechanical,
well-specified, cheap to verify) and what to keep (design work, delicate
refactors, anything security-sensitive), plus the `offload` script itself.

## Use it directly

```bash
bin/offload --dir ~/src/myrepo "Rename UserSvc to UserService across lib/ and fix all imports. Do not commit."
```

```
usage: offload [options] "task prompt"
  --dir DIR        working directory for the agent (default: current dir)
  --backend NAME   use one specific backend instead of the fallback chain
  --timeout SECS   per-backend time limit (default: 600)
  --list           show which backends are installed and exit

environment:
  OFFLOAD_BACKENDS   space-separated fallback order
                     (default: "opencode mimo grok codex")
  OFFLOAD_TIMEOUT    default per-backend time limit in seconds
```

Each backend runs headless with edits auto-approved inside the given
directory (`opencode run --auto`, `mimo run --dangerously-skip-permissions`,
`grok -p --permission-mode acceptEdits`, `codex exec --full-auto`). Point it
at a git checkout so every change is reviewable with `git diff`, and treat
the output like a contractor's patch: read it before you ship it.

## Why

Subscription and free tiers for coding agents are per-tool. If you have three
of them installed, that is three pools of cheap tokens sitting idle while
your primary model burns its own budget on bulk renames. offload turns those
pools into one queue with automatic failover.

## License

AGPL-3.0
