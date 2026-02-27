# Newton Skill Repository

This repository contains the Newton skill for AI Agent Skills. Use the FastSkill CLI to install and use it.

## Installation

From your project root:

```bash
fastskill add https://github.com/gonewton/skill.git
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for development and release details.

## What the skill covers

The Newton skill provides guidance for all Newton CLI commands and workflow graph operations:

**Classic loop commands:**
- `newton run` / `newton step` — start or single-step the eval-advisor-executor loop
- `newton batch <project_id>` — process queued plan files; `.conf` keys, hooks, control file contract
- `newton status` / `newton report` / `newton error` — inspect execution state
- `newton monitor` — live TUI for human-in-the-loop messages and approvals

**Workflow graph commands:**
- `newton workflow run|lint|validate|dot|explain|resume|checkpoints|artifacts|webhook`
- YAML schema: tasks, transitions, `$expr` conditions, `include_if`, `{{ ... }}` interpolation
- Macro authoring: `MacroDefinition` + `MacroInvocation` with `with:` params
- Execution settings: `parallel_limit`, `max_workflow_iterations`, `max_task_iterations`, `timeout_ms`
- Completion policy: `goal_gate`, `require_goal_gates`, `goal_gate_failure_behavior`
- Durability: checkpoint persistence, `newton workflow resume`, artifact routing/cleanup

**Built-in operators:**

| Operator | Purpose |
|---|---|
| `NoOpOperator` | Pass-through step; useful for routing and branching |
| `CommandOperator` | Run shell commands; captures stdout/stderr as JSON output |
| `SetContextOperator` | Deep-merge a patch object into the global workflow context |
| `ReadControlFileOperator` | Read and parse a JSON file from a path resolved at runtime |
| `AssertCompletedOperator` | Assert that a set of task IDs have completed before proceeding |
| `HumanApprovalOperator` | Pause for a boolean approve/reject decision from a human operator |
| `HumanDecisionOperator` | Pause for a multiple-choice selection from a human operator |

**Batch configuration:** `.newton/configs/<project_id>.conf` keys (`runner`, `workflow_file`, `pre_run_script`, tooling overrides, `resume`, `max_iterations`, `max_time`, `control_file`) and the `NEWTON_STATE_DIR`/`NEWTON_CONTROL_FILE` environment variable contract.

## License

Apache-2.0
