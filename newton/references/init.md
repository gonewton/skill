# newton init

## Purpose

Create a **Newton workspace**: `.newton/` layout, plan queue directories, default config stub, and template content installed via the statically linked **aikit-sdk** (the `aikit` binary is **not** required on `PATH`).

## Requirements

- Target path defaults to the current directory; it is created if missing and must be a directory.
- `.newton` must **not** already exist there (remove it or pick another path).
- Network access to GitHub for the default template source (not needed when `--template` points at a local path).

## Arguments

- **`PATH`** (optional positional): Directory to initialize (defaults to current directory; canonicalized to absolute).
- `--template <SOURCE>`: Template locator (GitHub slug, URL, or local path). Default: `gonewton/newton-templates`.

## What gets created

- `.newton/configs/`, `.newton/tasks/`, `.newton/plan/default/{todo,completed,failed,draft}/`, `.newton/state/`.
- `.newton/configs/default.conf` with `project_root`, `coding_model`, and a commented `workflow_file=` line. Set `workflow_file` (or `workflow_path`) when using `newton batch`.

## Example

```bash
newton init .

newton init /path/to/repo --template gonewton/newton-templates
```

## Next steps

```bash
newton run path/to/workflow.yaml --workspace .
```

See [configuration.md](configuration.md) for `.conf` keys and [batch.md](batch.md) for the plan queue.
