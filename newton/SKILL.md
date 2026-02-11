---
name: newton
description: Newton Loop anytime optimization framework. Use when running evaluator-advisor-executor cycles, configuring workspaces and GOAL.md, or operating newton run/step/batch/status/report/error from the CLI.
license: Apache-2.0
---

# Newton Loop

Newton Loop is an iterative optimization framework that runs evaluation, advice, and execution cycles until goals are met or limits are reached. Use this skill when you need to operate Newton from the CLI or set up workspaces and tools.

## When to use

- Running the full optimization loop or a single step.
- Inspecting execution status, generating reports, or diagnosing failures.
- Creating a workspace with GOAL.md and evaluator/advisor/executor tools.
- Documenting or explaining Newton subcommands and workflows.
- Observing live ailoop channels and moving work items through the monitor queue.

## Overview

- **Evaluator**: Assesses current state and provides quality metrics.
- **Advisor**: Suggests improvements based on evaluation.
- **Executor**: Applies recommendations.

The cycle repeats until goals are met or iteration/time limits are reached.

## Installation

```bash
# Homebrew (macOS/Linux)
brew tap gonewton/cli
brew install newton

# Scoop (Windows)
scoop bucket add gonewton https://github.com/gonewton/scoop-bucket
scoop install newton
```

Verify: `newton --help`

## Workspace setup

1. Create a directory and add `GOAL.md` (optimization goal).
2. Create `tools/` with scripts: `evaluator.sh`, `advisor.sh`, `executor.sh` (or override via CLI flags).
3. Run: `newton run ./workspace` or `newton step ./workspace`.

## Quick Start

1. Run `newton --help` to see the global description and available commands.
2. Inspect any subcommand with `newton <command> --help` to view supported options.
3. Refer to the reference sheets in this skill for typical workflows, required arguments, and example invocations.
4. Adjust values (paths, IDs, formats) to match your workspace before executing commands.

## Primary workflow

1. **newton run WORKSPACE**: Full optimization loop. See [references/run.md](references/run.md).
2. **newton step WORKSPACE**: One iteration. See [references/step.md](references/step.md).
3. **newton batch PROJECT_ID**: Process queued plans with hooks. See [references/batch.md](references/batch.md).
4. **newton status EXECUTION**: Inspect state. See [references/status.md](references/status.md).
5. **newton report EXECUTION**: Summarize results. See [references/report.md](references/report.md).
6. **newton error EXECUTION**: Diagnose failures. See [references/error.md](references/error.md).

## Quick reference

```bash
newton --help
newton run ./workspace --max-iterations 5 --max-time 120
newton step ./workspace --execution-id exec_456
newton batch my-project --workspace ./workspace --once
newton status exec_123 --workspace ./workspace
newton report exec_789 --workspace ./workspace --format json
newton error exec_999 --verbose
newton monitor
```

## Usage notes

- Strict mode: use `--evaluator-cmd`, `--advisor-cmd`, `--executor-cmd` to override tool binaries.
- Workspace and artifact paths must exist; ensure write permissions for `run` and `step`.
- For full flags and defaults: `newton <command> --help`.

## Helpful commands

```bash
cargo run -- --help
cargo run -- run ./workspace --max-iterations 2 --max-time 60
cargo run -- status exec_123 --workspace ./workspace
```

## References

- [references/run.md](references/run.md)
- [references/step.md](references/step.md)
- [references/batch.md](references/batch.md)
- [references/status.md](references/status.md)
- [references/report.md](references/report.md)
- [references/error.md](references/error.md)
- [references/monitor.md](references/monitor.md)

## Monitoring

- **newton monitor**: Watch ailoop channels and respond to blocking prompts. See [references/monitor.md](references/monitor.md).
