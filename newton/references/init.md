# newton init

## Purpose
Bootstrap a Newton workspace by creating the `.newton/` directory structure, rendering templates, and writing initial configuration files.

## Required Input
- `[PATH]`: Optional workspace path. Defaults to current directory if not provided.

## Important Flags
- `--template <NAME>`: Template to use for workspace initialization
- `--name <PROJECT_NAME>`: Project name for configuration
- `--coding-agent <AGENT>`: Coding agent (claude, codex, opencode)
- `--model <MODEL_SPEC>`: Model specification for coding agent
- `--interactive`: Prompt for confirmation/override of values
- `--force`: Overwrite existing `.newton/` directory

## Prerequisites
- **aikit**: Package manager required for template installation
  - Install: https://aikit.readthedocs.io
  - Used to: Fetch and render Newton templates

## What init creates

### Directory structure

```
workspace/
└── .newton/
    ├── templates/          # Installed via aikit
    ├── scripts/           # Rendered from template
    │   ├── evaluator.sh
    │   ├── advisor.sh
    │   └── executor.sh
    ├── state/             # Workspace-level state
    ├── tasks/             # Task-specific state (created per run)
    │   └── <task_id>/
    │       ├── input/
    │       ├── output/
    │       └── state/
    └── newton.toml       # Project configuration
```

### Initial files

- `GOAL.md`: Created if missing (optimization goal specification)
- `newton.toml`: Project configuration with defaults from template
- `scripts/evaluator.sh`: Evaluates current state
- `scripts/advisor.sh`: Suggests improvements
- `scripts/executor.sh`: Applies recommendations
- `scripts/run-tests.sh`: Test runner (optional, defaults to `cargo test` if absent)

## Templates

Templates are installed via `aikit` from the Newton package repository.

### Template locations

- Installed under `.newton/templates/<NAME>`
- Contains scripts and optional `newton.toml` shim
- Scripts are marked executable after rendering

### Default template

The default template provides:
- Basic evaluator, advisor, executor scripts
- Standard workspace structure
- Example `newton.toml` with common options

## Configuration options

### newton.toml format

```toml
[project]
name = "my-project"
template = "default"

[coding]
agent = "claude"
model = "sonnet"

[run]
max_iterations = 10
max_time = 300

[tools]
evaluator_cmd = ".newton/scripts/evaluator.sh"
advisor_cmd = ".newton/scripts/advisor.sh"
executor_cmd = ".newton/scripts/executor.sh"
```

### Interactive mode

When using `--interactive` flag:
1. Prompts for project name
2. Prompts for coding agent choice
3. Prompts for model specification
4. Shows template options
5. Confirms before writing files

### Force mode

When using `--force` flag:
- Overwrites existing `.newton/` directory
- Does not preserve existing configuration
- Use with caution

## Post-initialization

### Running from initialized workspace

After initialization, you can run Newton without specifying workspace path:

```bash
# From workspace root
newton run

# Explicit path (still works)
newton run ./workspace
```

### Workspace assumptions

- Current directory is workspace root when no path provided
- `.newton/scripts/evaluator.sh` is default evaluator
- `.newton/scripts/advisor.sh` is default advisor
- `.newton/scripts/executor.sh` is default executor

### Default test command

If `scripts/run-tests.sh` does not exist in the template:
- Evaluator uses `cargo test` as default test command
- Add `run-tests.sh` to override with custom test runner

## Examples

### Basic initialization

```bash
# Initialize current directory
newton init

# Initialize specific directory
newton init ./my-workspace
```

### With template

```bash
# Use specific template
newton init --template rust-web-service

# With custom template
newton init --template ./my-templates/custom
```

### With configuration

```bash
# Set agent and model
newton init --coding-agent claude --model sonnet

# Set project name
newton init --name my-awesome-project

# Combine options
newton init --name my-project --coding-agent codex --model gpt-5.1-codex-mini
```

### Interactive mode

```bash
# Interactive prompts for all options
newton init --interactive

# Interactive with specific template
newton init --template python-api --interactive
```

### Force overwrite

```bash
# Re-initialize existing workspace
newton init --force

# Force with template
newton init --template updated-template --force
```

## Common errors

### aikit not found

```
Error: aikit is required for newton init
Install from: https://aikit.readthedocs.io
```

**Solution**: Install aikit before running init

### Template not found

```
Error: template 'my-template' not found
```

**Solution**: Use `newton init --help` to list available templates

### Directory already exists

```
Error: .newton already exists
Use --force to overwrite
```

**Solution**: Use `--force` flag or remove existing `.newton/` directory

### Permission denied

```
Error: cannot create .newton/scripts/evaluator.sh: Permission denied
```

**Solution**: Ensure write permissions in target directory

## Best practices

1. **Commit initialization**: Add `.newton/` to version control for reproducibility
2. **Customize templates**: Create organization-specific templates for consistency
3. **Document run-tests.sh**: Provide clear test runner for your project type
4. **Version control config**: Track `newton.toml` changes
5. **Interactive for new projects**: Use `--interactive` for first-time setup
6. **Force with caution**: Only use `--force` when intentionally resetting workspace
