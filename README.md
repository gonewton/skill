# Newton Skill Repository

This repository contains the Newton skill for AI Agent Skills. Use the FastSkill CLI to install and use it.

## Installation

From your project root:

```bash
fastskill add https://github.com/gonewton/skill.git
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for development and release details.

The Newton skill includes guidance for `newton monitor`, classic loop commands (`run`, `step`, `batch`, `status`, `report`, `error`), and workflow-graph operations (`workflow run/lint/explain/resume/checkpoints/artifacts/webhook`). Batch documentation calls out `.newton/configs/<project_id>.conf` keys (`runner`, `workflow_file`, `pre_run_script`, tooling overrides, `resume`, `max_iterations`, `max_time`, and `control_file`) plus the `NEWTON_STATE_DIR`/`NEWTON_CONTROL_FILE` contract.

## License

Apache-2.0
