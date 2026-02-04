# newton status

## Purpose

Shows active phase, iteration counts, and blockers for a given execution (read-only).

## Required Input

- `EXECUTION`: Execution identifier to inspect.

## Important Flags

- `--workspace <PATH>`: workspace location (defaults to current directory).

## Example

```bash
newton status exec_123 --workspace ./workspace
```

## Tips

- `newton status --help` lists every option and default.
- Set `--workspace` when querying executions outside the current directory.
- Status only reads artifacts; it does not mutate files.
