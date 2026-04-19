---
name: newton
description: Newton CLI for workflow YAML graphs (operators, checkpoints, goal gates), batch plan queues, ailoop human-in-the-loop via monitor, and HTTP APIs via serve. Use when running or resuming workflows, validating or linting workflow files, managing checkpoints or artifacts, configuring .newton/configs, or using validate, lint, explain, dot, resume, checkpoints, artifacts, webhook, monitor, or batch.
license: Apache-2.0
compatibility: Requires the newton binary on PATH. newton init requires aikit on PATH for templates. newton monitor requires a running ailoop server unless your workflow docs say otherwise.
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
2. `newton init [PATH]` to create `.newton/` and install the template via `aikit` (PATH defaults to the current directory).
3. `newton run <workflow.yaml> --workspace <root>` (optional second positional input file for trigger payload).

## CLI commands (source order)

These subcommands match the current CLI (confirm with `newton --help` on your build):

| Command | Role |
| --- | --- |
| `run` | Execute a workflow graph from YAML |
| `init` | Create `.newton/` and install the default template |
| `batch` | Process queued plans under `.newton/plan/<project_id>/` |
| `serve` | HTTP/WebSocket API for workflow state and streaming |
| `monitor` | Terminal UI for ailoop HIL channels |
| `validate` | Validate workflow YAML before run |
| `dot` | Emit Graphviz DOT for the workflow graph |
| `lint` | Best-practice checks on a workflow file |
| `explain` | Human-readable description of workflow behavior |
| `resume` | Continue from a checkpoint (`--execution-id`) |
| `checkpoints` | `list` / `clean` checkpoint data |
| `artifacts` | `clean` old execution artifacts |
| `webhook` | `serve` or `status` for webhook-triggered runs |

For commands without a dedicated reference file below, use `newton <cmd> --help` as the source of truth for flags and examples.

There is **no** `step`, `status`, `report`, or `error` subcommand in current releases. Inspect runs via **checkpoints**, **resume**, **artifacts**, workflow logs, and `.newton/tasks/` under the project workspace. See [references/step.md](references/step.md) and related stubs for migration hints.

## Typical flows

1. **New workspace**: `newton init .` then set `workflow_file` in `.newton/configs/default.conf` when using batch; run workflows with `newton run path/to/workflow.yaml --workspace .`.
2. **Queue of plans**: Configure `.newton/configs/<project_id>.conf` with `project_root` and `workflow_file`; place plans in `.newton/plan/<project_id>/todo/`; run `newton batch <project_id>`.
3. **Live HIL**: Start [ailoop](https://github.com/goailoop/ailoop), point `.newton/configs/monitor.conf` at HTTP and WebSocket URLs (or pass `--http-url` / `--ws-url`), then `newton monitor`.
4. **API / dashboards**: `newton serve` exposes REST, WebSocket, and SSE endpoints for workflow instances and streams (see `newton serve --help` and the Newton repository `README.md` when updated).

## Usage notes

- `newton init` requires `aikit` on `PATH` and refuses to run if `.newton` already exists (remove it or pick another directory).
- `newton run` resolves the workflow path from `--file` if set, otherwise the first positional argument.
- `--server <URL>` on `newton run` registers the run with a Newton API instance started via `newton serve` for lifecycle notifications.
- Checkpoint and artifact layouts live under `.newton/` inside the workspace you pass with `--workspace` (or the discovered project root for batch).

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

**Canonical skill:** agent instructions for Newton CLI are maintained in [gonewton/skill](https://github.com/gonewton/skill) (`newton/`). Prefer `newton <cmd> --help` when behavior differs by version.

Organization-specific shell or YAML that sources the same `.conf` files (extra keys, `develop` wrappers) is **not** documented here; keep that in your own workspace skill or internal docs.
