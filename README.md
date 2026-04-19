# Newton Skill Repository

This repository contains the **Newton** skill for [Agent Skills](https://agentskills.io/). Use the FastSkill CLI to install it.

## Installation

From your project root:

```bash
fastskill add https://github.com/gonewton/skill.git
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for development and release details.

## What the skill covers

The skill documents the **workflow-first** Newton CLI: YAML workflow graphs (operators, checkpoints, goal gates), workspace layout under `.newton/`, and command-line usage aligned with `newton --help`.

**Commands and topics** (see [`newton/SKILL.md`](newton/SKILL.md) for the full table and flows):

- **Run and inspect workflows:** `run`, `validate`, `lint`, `explain`, `dot`, `resume`, `checkpoints`, `artifacts`
- **Workspace and queues:** `init`, `batch` (plan queue under `.newton/plan/<project_id>/`), `.newton/configs` keys in [references/configuration.md](newton/references/configuration.md)
- **APIs and HIL:** `serve`, `monitor` (with [ailoop](https://github.com/goailoop/ailoop)), `webhook`

Current releases do **not** expose top-level `step`, `status`, `report`, or `error` subcommands. The skill includes short [references](newton/references/) stubs explaining what to use instead.

**Reference layout:** [`newton/references/`](newton/references/) holds focused sheets (`configuration`, `init`, `run`, `batch`, `monitor`) plus stubs for removed command names. Deeper workflow YAML semantics (tasks, operators, macros) live in the Newton repository documentation; this skill centers on CLI and workspace configuration.

## License

Apache-2.0
