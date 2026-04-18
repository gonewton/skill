---
name: newton
description: Newton CLI for iterative optimization and workflow automation. Use when running workflow graphs (newton run or batch), configuring workspaces and GOAL.md, and operating validate, lint, explain, resume, checkpoints, artifacts, serve, monitor, or webhook.
license: Apache-2.0
---

# Newton

Newton is a **workflow-first** CLI: YAML workflow graphs with operators, checkpoints, artifacts, and goal gates. Classic evaluator, advisor, and executor-only loops are expressed inside workflows now, not as separate top-level commands.

## When to use

- Running or resuming workflows, batch plan queues, or webhook-driven runs.
- Initializing a workspace (`newton init`) and editing `.newton/configs/*.conf`.
- Validating or explaining workflow YAML; cleaning checkpoints or artifacts.
- Operating `newton monitor` against ailoop, or `newton serve` for HTTP or WebSocket APIs.

## Installation

```bash
brew tap gonewton/cli
brew install newton

scoop bucket add gonewton https://github.com/gonewton/scoop-bucket
scoop install newton
```

Verify: `newton --help` and `newton --version`.

## Quick start

1. `newton --help` and `newton <command> --help` for flags.
2. `newton init .` in an existing empty-target directory (requires `aikit` on `PATH`).
3. `newton run <workflow.yaml> --workspace <root>` (optional second positional input file for trigger payload).

## Primary commands (current CLI)

Use the built-in help for the exact list on your installed version. Typical workflow-oriented commands include:

`run`, `init`, `batch`, `serve`, `monitor`, `validate`, `dot`, `lint`, `explain`, `resume`, `checkpoints`, `artifacts`, `webhook`.

There is **no** `step`, `status`, `report`, or `error` subcommand in current releases. Inspect runs via **checkpoints**, **resume**, **artifacts**, workflow logs, and `.newton/tasks/` layout under the project workspace.

## Quick reference

```bash
newton run workflow.yaml --workspace . --verbose
newton batch my-project --workspace ~/ws --once
newton validate workflow.yaml
newton lint workflow.yaml
newton explain workflow.yaml
newton resume --execution-id <uuid> --workspace .
newton monitor
```

## References

- [references/configuration.md](references/configuration.md) — `.newton/configs` keys read by Newton (`batch`, `monitor`, `init` stub)
- [references/init.md](references/init.md)
- [references/run.md](references/run.md)
- [references/batch.md](references/batch.md)
- [references/monitor.md](references/monitor.md)

The in-repo skill under `gonewton/newton/skills/newton-cli-commands/` mirrors CLI-focused sheets; prefer `newton <cmd> --help` when behavior differs by version.

Organization-specific shell or YAML that sources the same `.conf` files (extra keys, `develop` wrappers) is **not** documented here; keep that in your own workspace skill or internal docs.
