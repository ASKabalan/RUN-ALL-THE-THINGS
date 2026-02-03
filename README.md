# RUN-ALL-THE-THINGS

A centralized workflow dispatcher that allows you to trigger workflows across multiple repositories with ease.

## Purpose

This repository serves as a central hub to trigger GitHub Actions workflows in your other repositories. Instead of manually navigating to each repository to trigger workflows, you can trigger them all from one place.

## Features

- **Manual Trigger**: Easy workflow dispatch through GitHub Actions UI
- **Repository Selection**: Choose specific repositories or use pre-configured list
- **Workflow Selection**: Select which workflows to run in each repository
- **Branch Targeting**: Run workflows against any branch (defaults to `main`)
- **Configurable**: Use `config.json` for default settings or override via UI inputs

## Quick Start

### 1. Configure Your Repositories

Edit `config.json` to specify your default repositories and workflows:

```json
{
  "repositories": [
    "your-username/repo1",
    "your-username/repo2"
  ],
  "workflows": [
    "ci.yml",
    "tests.yml"
  ]
}
```

### 2. Set Up GitHub Token

**Important**: For this to work across different repositories, you need a GitHub Personal Access Token (PAT) with `workflow` permissions:

1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate a new token with the `workflow` scope (and `repo` scope if repositories are private)
3. Add it as a repository secret named `GH_TOKEN` in this repository:
   - Go to this repository's Settings → Secrets and variables → Actions
   - Click "New repository secret"
   - Name: `GH_TOKEN`
   - Value: Your generated token

**Alternative for same organization**: If you only want to trigger workflows within repositories in the same organization/user account, you can modify the workflow to use `secrets.GITHUB_TOKEN`, but this may have limitations based on your organization settings.

### 3. Trigger Workflows

Go to the **Actions** tab in this repository:

1. Select "Trigger Workflows" from the workflow list
2. Click "Run workflow"
3. Optionally override:
   - **Repositories**: Comma-separated list (e.g., `owner/repo1,owner/repo2`)
   - **Workflows**: Comma-separated list (e.g., `ci.yml,tests.yml`)
   - **Branch**: Target branch (defaults to `main`)

## Configuration

### config.json

The `config.json` file contains default settings:

- **repositories**: Array of repository names in `owner/repo` format
- **workflows**: Array of workflow file names (must match the workflow file names in target repositories)

### Workflow Inputs

You can override config.json settings when manually triggering:

- **repositories**: Comma-separated repository list (overrides config)
- **workflows**: Comma-separated workflow list (overrides config)
- **branch**: Target branch for workflow execution (default: `main`)

## Permissions

The workflow requires:

- `workflow` permission to trigger workflows in other repositories
- Access to target repositories (must be owner or have appropriate permissions)

If using `GITHUB_TOKEN` instead of a PAT:
- Only works for repositories in the same organization
- May have limited permissions depending on organization settings

## Example Usage

### Using config.json defaults

Simply run the workflow without any inputs - it will use repositories and workflows from `config.json`.

### Triggering specific repositories

Input in the UI:
- **repositories**: `ASKabalan/my-app,ASKabalan/my-api`
- **workflows**: `deploy.yml`
- **branch**: `main`

### Triggering on a different branch

Input in the UI:
- **branch**: `develop`

This will trigger all configured workflows on the `develop` branch.

## How It Works

1. The workflow reads `config.json` for default repositories and workflows
2. User inputs (if provided) override the config defaults
3. The workflow uses GitHub CLI (`gh`) to trigger each workflow in each repository
4. Results are displayed in the workflow logs and summary

## Prerequisites

Your target repositories must:

- Have GitHub Actions enabled
- Have the workflows you want to trigger (e.g., `ci.yml`, `tests.yml`)
- Have these workflows configured with `workflow_dispatch` trigger (required for manual triggering)

## Contributing

Feel free to customize `config.json` and the workflow to fit your needs!

## Troubleshooting

**Workflow not triggering?**
- Ensure the `GH_TOKEN` secret is properly configured in repository settings
- Ensure the workflow file name matches exactly (case-sensitive)
- Check that the target repository has the specified workflow
- Verify your GitHub token has the correct permissions (workflow scope)
- Ensure the workflow in the target repo has `workflow_dispatch` enabled

**Permission denied?**
- Make sure you have a valid PAT with `workflow` scope (and `repo` for private repos)
- Verify you have access to the target repositories
- Check that the `GH_TOKEN` secret is set in this repository's settings
