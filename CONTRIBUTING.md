# Contributing to Newton Skill

## Repository structure

```
gonewton/skill/
├── skill-project.toml       # Project-level configuration
└── newton/                   # Newton skill (canonical source)
    ├── SKILL.md              # Skill documentation
    ├── skill-project.toml    # Skill metadata
    └── references/           # CLI and config references
        ├── configuration.md
        ├── init.md
        ├── run.md
        ├── batch.md
        ├── monitor.md
        ├── step.md           # removed-command stub
        ├── status.md
        ├── report.md
        └── error.md
```

## Editing CLI content

**Edit this repository first.** The published skill under `gonewton/skill/newton/` is the single source of truth for Newton CLI agent instructions.

## Development

1. Edit files under `newton/`
2. Commit and push to the `main` branch
3. The CI/CD workflow packages and publishes a new release when `newton/**` or the workflow file changes

## CI/CD

The GitHub Actions workflow in `.github/workflows/publish-skill.yml`:

- Triggers on push to `main` when paths `newton/**` or `.github/workflows/publish-skill.yml` change, or on `workflow_dispatch`
- Packages the skill using the FastSkill CLI
- Creates a GitHub release with the packaged skill artifact
- Uses a GitHub App token for release operations (delete old releases)

## Required secrets

Configure these repository secrets for the publish workflow:

- `GH_APP_ID`: GitHub App ID for token generation
- `GH_APP_PRIVATE_KEY`: GitHub App private key for authentication
- `GITHUB_TOKEN`: Provided automatically by GitHub Actions

Ensure the GitHub App has **Repository: Contents** read and write on this repository.
