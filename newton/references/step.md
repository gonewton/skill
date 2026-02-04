# newton step

## Purpose

Executes one iteration of the optimization loop (evaluation, advice, execution) without running until convergence.

## Required Input

- `WORKSPACE`: Path where Newton artifacts are stored.

## Important Flags

- `--execution-id <ID>`: associate the step with a tracking identifier.

## Example

```bash
newton step ./workspace --execution-id exec_456
```

## Tips

- Use `newton step --help` for all optional arguments.
- `--execution-id` is optional but recommended for log correlation.
- Workspace should already contain artifacts from a previous run or step.
