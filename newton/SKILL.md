---
name: newton
description: Newton CLI for workflow YAML graphs (operators, checkpoints, goal gates), the autonomous optimization loop (grade → reconcile → change-request → plan → develop → re-grade) over the Finding → Change Request → Plan → Execution spine with Graders/Assessments, ailoop human-in-the-loop via monitor, and HTTP APIs via serve. Use when running or resuming workflows, driving or observing the optimization loop, validating or linting workflow files, managing checkpoints or artifacts, configuring .newton/configs, or using optimize, validate, lint, explain, dot, resume, checkpoints, artifacts, or monitor.
license: Apache-2.0
compatibility: Requires the newton binary on PATH. newton monitor requires a running ailoop server unless your workflow docs say otherwise.
---

# Newton

Newton is a **workflow-first** CLI: YAML workflow graphs with operators, checkpoints, artifacts, and goal gates. Classic evaluator, advisor, and executor-only loops are expressed inside workflows now, not as separate top-level commands. **Sub-workflows** are supported: a task can invoke another workflow file with `WorkflowOperator` (`workflow_path`, optional `context` and `triggers` merges), subject to workspace path rules and a maximum nesting depth.

## When to use

- Running or resuming workflows (including graphs that call nested workflows via `WorkflowOperator`).
- Driving the **optimization loop** for a project (`.newton/scripts/optimize.sh` / `newton optimize`) or observing it over `serve`; working with loop entities (Finding, Change Request, Plan, Assessment).
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
2. `newton init [PATH]` to create `.newton/` and install the template via the bundled aikit-sdk (PATH defaults to the current directory).
3. `newton run <workflow.yaml> --workspace <root>` (optional second positional input file for trigger payload).

## CLI commands (source order)

These subcommands match the current CLI (confirm with `newton --help` on your build):

| Command | Role |
| --- | --- |
| `run` | Execute a workflow graph from YAML |
| `init` | Create `.newton/` and install the default template |
| `optimize` | Drive the optimization loop / drain the Plan queue under `.newton/plan/<project_id>/` (renamed from `batch`, ADR 0003). The full closed loop runs via `.newton/scripts/optimize.sh` until the in-process driver lands (spec 073) |
| `serve` | HTTP API: parity REST (dashboard, portfolio, workflows, HIL, etc.), WebSocket/SSE streams (see references) |
| `monitor` | Terminal UI for ailoop HIL channels |
| `validate` | Validate workflow YAML before run |
| `dot` | Emit Graphviz DOT for the workflow graph |
| `lint` | Best-practice checks on a workflow file |
| `explain` | Human-readable description of workflow behavior |
| `resume` | Continue from a checkpoint (`--execution-id`) |
| `checkpoints` | `list` / `clean` checkpoint data |
| `artifacts` | `clean` old execution artifacts |

> **Removed:** `webhook` (ADR 0004 — the optimizer is self-driving, no external HTTP ingress) and `health` (folded into `doctor`). **Note:** newer builds move `validate`/`lint`/`explain`/`graph`/`resume`/`runs` under a `workflow` subcommand (e.g. `newton workflow validate`); confirm with `newton --help` on your build.

For commands without a dedicated reference file below, use `newton <cmd> --help` as the source of truth for flags and examples.

There is **no** `step`, `status`, `report`, or `error` subcommand in current releases. Inspect runs via **checkpoints**, **resume**, **artifacts**, workflow logs, and `.newton/tasks/` under the project workspace. See [references/step.md](references/step.md) and related stubs for migration hints.

## Typical flows

1. **New workspace**: `newton init .` then set `workflow_file` in `.newton/configs/default.conf` when using batch; run workflows with `newton run path/to/workflow.yaml --workspace .`.
2. **Optimization loop**: Configure the `optimize_*` block in `.newton/configs/<project_id>.conf` (see [Optimization loop](#optimization-loop)); run the closed loop with `.newton/scripts/optimize.sh <project_id> [--once]`. (`newton optimize <project_id>` currently drains the Plan queue.)
3. **Live HIL**: Start [ailoop](https://github.com/goailoop/ailoop), point `.newton/configs/monitor.conf` at HTTP and WebSocket URLs (or pass `--http-url` / `--ws-url`), then `newton monitor`.
4. **API / dashboards**: `newton serve` exposes the full parity REST surface (products, components, opportunities, plans, workflows, persistence, …) plus stream endpoints. **`newton serve --help` is incomplete** as of current releases; use [references/serve-api.md](references/serve-api.md) and the OpenAPI file linked there as the contract.

## Usage notes

- `newton init` does not require `aikit` on `PATH` (templates install via the statically linked aikit-sdk); it refuses to run if `.newton` already exists (remove it or pick another directory). It needs network access to GitHub unless `--template` points at a local path.
- `newton run` resolves the workflow path from `--file` if set, otherwise the first positional argument.
- `--server <URL>` on `newton run` registers the run with a Newton API instance started via `newton serve` for lifecycle notifications.
- Checkpoint and artifact layouts live under `.newton/` inside the workspace you pass with `--workspace` (or the discovered project root for batch).

## Optimization loop

Newton's reason to exist is an **autonomous, GitHub-free loop** that improves a project toward a project-defined **Grade**:

```
grade ─→ reconcile ─→ change-request ─→ (approve) ─→ plan ─→ develop ─→ merge ─→ re-grade
(Assessment)(Findings)(Change Request)              (HOW)  (tests)   (local git)
```

Durable spine (in Newton's store, never a board): `Finding → Change Request → Plan → Execution`.

- **Grader → Assessment** — a **Grader** (a command program at `.newton/grader/<name>/generate.sh`, or a rubric agent) inspects the repo and **prints an Assessment** (overall Grade + per-dimension Scores + Observations). `GraderCommandOperator` persists it; the script must NOT self-persist.
- **Reconciliation → Finding** — `ReconcileOperator` turns Observations into durable **Findings** (refresh / create / resolve).
- **Change Request → Plan** — `ChangeRequestOperator` synthesizes one CR over the Findings (`decision: propose|none`); the planner enriches it into a **Plan** (the HOW; status `draft→ready→running→complete|failed`, plus `abandoned`).
- **Optimize Run / Cycle / Trajectory** — one loop invocation is an Optimize Run; each iteration a Cycle; the per-cycle log the Trajectory.
- **Break conditions** — `converged` / `stalled_on_blocked` / `max_cycles` / per-grader `target` (all clear) / per-grader `regression` (any drops) / `no_progress`.
- **`blocked` Finding + un-block** — a Plan that fails develop after `optimize_max_failed_attempts` quarantines its Findings (`blocked`); a human un-blocks via `POST /api/v1/findings/{id}/unblock`. The loop keeps optimizing the rest.

Driver: `.newton/scripts/optimize.sh <project_id> [--once] [--max-cycles N] [--delivery local|pr] [--auto-approve]`, reading the `optimize_*` block (`optimize_repo_id`, `optimize_repo_path`, `optimize_test_cmd`, `optimize_graders`, `optimize_max_cycles`, `optimize_converge_rounds`, `optimize_target_grade[_<g>]`, `optimize_regression_tolerance[_<g>]`, `optimize_max_failed_attempts`, `optimize_auto_approve`, `delivery`).

Observe over `serve` (read-only — self-driving, ADR 0004): `GET /api/v1/optimize-runs[/{id}/trajectory|/cycles]`, `GET /api/v1/findings?status=blocked`, `POST /api/v1/findings/{id}/unblock`. See [references/optimize.md](references/optimize.md) and the Newton `CONTEXT.md` glossary.

## Quick reference

```bash
newton run workflow.yaml --workspace . --verbose
.newton/scripts/optimize.sh my-project --once     # drive one closed-loop cycle
newton optimize my-project --workspace ~/ws --once  # Rust command: drain the Plan queue
newton validate workflow.yaml
newton lint workflow.yaml
newton explain workflow.yaml
newton resume --execution-id <uuid> --workspace .
newton monitor
```

## References

- [references/serve-api.md](references/serve-api.md) — `newton serve` REST paths, OpenAPI link, streams note
- [references/configuration.md](references/configuration.md) — `.newton/configs` keys read by Newton (`optimize_*`, `monitor`, `init` stub)
- [references/init.md](references/init.md)
- [references/run.md](references/run.md)
- [references/optimize.md](references/optimize.md) — the optimize command + closed optimization loop, entities, break conditions, serve endpoints (supersedes `batch.md`)
- [references/monitor.md](references/monitor.md)

**Canonical skill:** agent instructions for Newton CLI are maintained in [gonewton/skill](https://github.com/gonewton/skill) (`newton/`). Prefer `newton <cmd> --help` when behavior differs by version.

Organization-specific shell or YAML that sources the same `.conf` files (extra keys, `develop` wrappers) is **not** documented here; keep that in your own workspace skill or internal docs.
