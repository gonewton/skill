# newton report

## Purpose

Generates a textual or JSON summary of results, recommendations, and anomalies for a given execution.

## Required Input

- `EXECUTION`: Execution identifier to summarize.

## Important Flags

- `--workspace <PATH>`: workspace containing source artifacts (default `.`).
- `--format <text|json>`: output representation (default `text`).

## Example

```bash
newton report exec_789 --workspace ./workspace --format json
```

## Tips

- Use `--format text` for human-readable output, `--format json` for scripting.
- Execution ID must exist from a prior run or step.
