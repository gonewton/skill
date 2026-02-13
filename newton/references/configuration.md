# Newton Configuration

This reference documents the complete configuration format for Newton batch workflows.

## Configuration file format

Newton batch reads configuration from `.newton/configs/<project_id>.conf` files. The format is simple key=value pairs with line comments starting with `#`.

### Example configuration file

```bash
# Project configuration
project_root=/path/to/project
coding_agent=claude
coding_model=sonnet

# Tool paths (optional)
evaluator_cmd=/path/to/project/.newton/evaluator.sh
advisor_cmd=/path/to/project/.newton/advisor.sh
executor_cmd=/path/to/workspace/.newton/scripts/executor.sh

# Run control (optional)
max_iterations=5
max_time=3600
control_file=newton_control.json
resume=true

# Hooks (optional)
pre_run_script=/path/to/pre-run.sh
post_success_script=/path/to/post-success.sh
post_fail_script=/path/to/post-fail.sh
```

## Required configuration keys

### project_root
Absolute or relative path to the project directory containing the `.newton` folder with evaluator, advisor, and executor scripts. Relative paths are resolved against the workspace root.

- Type: Path (absolute or relative)
- Example: `/home/user/projects/my-app` or `./my-app`

### coding_agent
The name of the AI agent to use for code generation.

- Type: String
- Common values: `claude`, `codex`, `opencode`
- Example: `coding_agent=claude`

### coding_model
The model specification for the coding agent.

- Type: String
- Format: Depends on agent (e.g., `sonnet`, `gpt-5.1-codex-mini`, `zai-coding-plan/glm-4.7`)
- Example: `coding_model=sonnet`

## Optional configuration keys

### Tool override keys

#### evaluator_cmd
Path to the evaluator script that assesses current state.

- Type: Path (absolute recommended)
- Default: `project_root/.newton/scripts/evaluator.sh` or `project_root/.newton/evaluator.sh`
- Example: `evaluator_cmd=/home/user/project/.newton/evaluator.sh`

#### advisor_cmd
Path to the advisor script that suggests improvements.

- Type: Path (absolute recommended)
- Default: `project_root/.newton/scripts/advisor.sh` or `project_root/.newton/advisor.sh`
- Example: `advisor_cmd=/home/user/project/.newton/advisor.sh`

#### executor_cmd
Path to the executor script that applies changes.

- Type: Path (absolute recommended)
- Default: `workspace_root/.newton/scripts/executor.sh`
- Example: `executor_cmd=/home/user/workspace/.newton/scripts/executor.sh`

### Run control keys

#### max_iterations
Maximum number of optimization iterations before stopping.

- Type: Integer
- Default: 10
- Example: `max_iterations=5`

#### max_time
Maximum wall-clock time in seconds before stopping.

- Type: Integer (seconds)
- Default: 300
- Example: `max_time=3600`

#### control_file
Name of the control file used to signal completion.

- Type: Filename (not full path)
- Default: `newton_control.json`
- Location: Stored under `NEWTON_STATE_DIR`
- Usage: Evaluator writes `{"done": true}` to signal success
- Example: `control_file=newton_control.json`

#### resume
Preserve task state directories between runs instead of wiping them.

- Type: Boolean (true/false, 1/0, case-insensitive)
- Default: false
- Values: `true`, `false`, `1`, `0`
- Example: `resume=true`

### Hook script keys

#### pre_run_script
Script to run once per plan before `newton run`.

- Type: Path (absolute recommended)
- Executes via: `sh -c "<value>"`
- Working directory: Project root
- Environment: See Environment Variables section
- Common use: Git checkout, stash, pull, branch creation
- Example: `pre_run_script=/home/user/.newton/scripts/pre-run.sh`

#### post_success_script
Script to run after a successful `newton run`.

- Type: Path (absolute recommended)
- Executes via: `sh -c "<value>"`
- Working directory: Project root
- Environment: `NEWTON_RESULT=success` plus other batch variables
- Exit codes: 0 moves plan to `completed/`, non-zero moves to `failed/`
- Common use: Commit, push, create PR, notify
- Example: `post_success_script=/home/user/.newton/scripts/post-success.sh`

#### post_fail_script
Script to run after a failed `newton run`.

- Type: Path (absolute recommended)
- Executes via: `sh -c "<value>"`
- Working directory: Project root
- Environment: `NEWTON_RESULT=failure` plus other batch variables
- Exit codes: Ignored (plan always moves to `failed/`)
- Common use: Cleanup, notify, delete branches
- Example: `post_fail_script=/home/user/.newton/scripts/post-fail.sh`

## Environment variables

The following environment variables are available to hook scripts and tools:

### Core batch variables

- `NEWTON_WS_ROOT`: Absolute path to workspace root
- `NEWTON_PROJECT_ROOT`: Absolute path to project root
- `NEWTON_PROJECT_ID`: Batch project identifier (from config file name)
- `NEWTON_TASK_ID`: Sanitized plan filename used for task directory
- `NEWTON_GOAL_FILE`: Path to generated goal spec for current run
- `NEWTON_STATE_DIR`: Path to task state directory
- `NEWTON_CONTROL_FILE`: Path to control file
- `NEWTON_RESUME`: Value of `resume` config key
- `NEWTON_CODER_CMD`: Path to coder script
- `NEWTON_BRANCH_NAME`: Feature branch name
- `NEWTON_BASE_BRANCH`: Base branch (main, master, etc.)

### Coding agent variables

- `CODING_AGENT`: Coding agent name from config
- `CODING_AGENT_MODEL`: Coding model specification from config
- `NEWTON_EXECUTOR_CODING_AGENT`: Executor agent name (if configured)
- `NEWTON_EXECUTOR_CODING_AGENT_MODEL`: Executor model (if configured)

### Hook-specific variables

- `NEWTON_RESULT`: Set to `success` or `failure` when running post hooks
- `NEWTON_EXECUTION_ID`: Available in `after_run` hooks

## Validation

Batch validates configuration before processing plans:

1. Checks that config file exists at `.newton/configs/<project_id>.conf`
2. Verifies `project_root`, `coding_agent`, and `coding_model` are non-empty
3. Ensures `project_root` contains a `.newton` directory
4. Resolves relative paths against workspace root

Missing required keys or invalid paths cause batch to exit with an error listing available configs.

## Branch naming

Feature branches are determined in this order:

1. `branch:` frontmatter in the plan spec (YAML frontmatter)
2. `NEWTON_BRANCH_NAME` environment variable
3. `feature/<task_id>` (underscores in task_id become dashes)

Example plan spec frontmatter:

```yaml
---
branch: feature/add-user-authentication
---
```

## Common patterns

### Minimal configuration

```bash
project_root=./my-project
coding_agent=claude
coding_model=sonnet
```

### Full configuration with all options

```bash
# Project
project_root=/home/user/projects/my-app
coding_agent=claude
coding_model=sonnet

# Tools
evaluator_cmd=/home/user/projects/my-app/.newton/evaluator.sh
advisor_cmd=/home/user/projects/my-app/.newton/advisor.sh

# Limits
max_iterations=10
max_time=1800
control_file=newton_control.json
resume=true

# Hooks
pre_run_script=/home/user/.newton/scripts/pre-run.sh
post_success_script=/home/user/.newton/scripts/post-success.sh
post_fail_script=/home/user/.newton/scripts/post-fail.sh
```

### CI/CD configuration

```bash
project_root=/ci/projects/api-service
coding_agent=opencode
coding_model=zai-coding-plan/glm-4.7

pre_run_script=/ci/hooks/pre-checkout.sh
max_iterations=5
max_time=3600
post_success_script=/ci/hooks/notify-slack.sh
```
