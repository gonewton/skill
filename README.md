# Newton Skill Repository

This repository contains the Newton skill for AI Agent Skills. Use the FastSkill CLI to install and use it.

## Installation

From your project root:

```bash
fastskill add https://github.com/gonewton/skill.git
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for development and release details.

The Newton skill now includes guidance for the new `newton monitor` command alongside `run`, `step`, `status`, `report`, and `error`. The batch documentation calls out the new `.newton/configs/<project_id>.conf` keys (`pre_run_script`, tooling overrides, `resume`, `max_iterations`, `max_time`, and `control_file`) as well as the `NEWTON_STATE_DIR`/`NEWTON_CONTROL_FILE` contract that gatekeeps success.

## License

Apache-2.0
