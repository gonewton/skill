# newton run

## Purpose

Runs the complete Newton optimization loop until limits defined in RunArgs are met.

## Required Input

- `WORKSPACE`: Path to the workspace directory containing Newton manifests.

## Important Flags

- `--max-iterations <N>`: stop after N iterations (default 10).
- `--max-time <SECONDS>`: wall-clock cap (default 300).
- `--evaluator-cmd`, `--advisor-cmd`, `--executor-cmd`: override tool binaries for strict mode.
- `--evaluator-status-file`, `--advisor-recommendations-file`, `--executor-log-file`: redirect artifact paths.
- `--tool-timeout-seconds` and per-tool `--*-timeout` overrides.

## Example

```bash
newton run ./workspace --max-iterations 5 --max-time 120
```

## Tips

- `newton run --help` shows every flag and default.
- Strict-mode overrides must point to executable binaries on PATH.
- Pre-create parent directories when redirecting artifact files.
