# newton run

## Purpose

Execute a **workflow graph** from a YAML file: tasks run according to dependencies, operators, checkpoints, goal gates, and completion policy.

## Arguments

- **`WORKFLOW`** (positional, optional if `--file` is set): Path to the workflow YAML.
- **`INPUT_FILE`** (optional second positional): Stored in the trigger payload as `input_file` for workflows that expect a spec path.

## Important options

- `--file <PATH>`: Workflow path (overrides positional `WORKFLOW` when both are set).
- `--workspace <PATH>`: Workspace root (default: current directory). Checkpoints and artifacts resolve under this tree.
- `--arg KEY=VALUE`: Merge into `triggers.payload` (repeatable). Values may use `@path` to read file contents; use `@@` for a literal `@`.
- `--set KEY=VALUE`: Merge into workflow context at runtime (repeatable).
- `--trigger-json <PATH>`: Load a JSON object as the base trigger payload before `--arg` merges.
- `--parallel-limit N`, `--max-time-seconds N`, `--verbose`, `--server <URL>`: See `newton run --help`.

## Examples

```bash
newton run workflow.yaml --workspace .

newton run workflow.yaml input/spec.md --workspace ./proj --arg env=prod

newton run --file ./workflows/ci.yaml --workspace . --verbose
```

## Notes

- Validate and inspect first: `newton validate`, `newton lint`, `newton explain` on the same file.
- Resume: `newton resume --execution-id <uuid>` (and related checkpoint commands).
- The classic `newton run WORKSPACE` loop (evaluator or advisor or executor scripts only) is not the current execution model; express loops in workflow YAML instead.
