# newton error

## Purpose

Diagnoses failures for a specific execution: stack traces, tool crashes, incompatible artifacts.

## Required Input

- `EXECUTION`: Execution identifier to debug.

## Important Flags

- `--verbose`: include stack traces, raw logs, and contextual artifacts.

## Example

```bash
newton error exec_999 --verbose
```

## Tips

- Run `newton error --help` for the full list of options.
- Capture command output; it often points to failing artifacts.
