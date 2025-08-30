# GitHub Actions Branch for oh-my-rime Fork Sync

This branch (`gh-actions`) is a dedicated branch for managing GitHub Actions workflows, specifically for automatically syncing this fork with the upstream repository.

## Purpose

This branch serves as the **default branch** to enable GitHub Actions workflows to be visible and manually triggerable in the Actions tab. It contains only the necessary workflow files and documentation, keeping it separate from the main codebase.

## Architecture

```
Repository Structure:
├── gh-actions (default branch) ← You are here
│   └── .github/workflows/
│       └── sync-upstream.yaml  ← Automated sync workflow
└── main (code branch)
    └── [actual project files]  ← Receives updates from upstream
```

## How It Works

### 1. Branch Strategy
- **`gh-actions`**: Default branch containing only GitHub Actions workflows
- **`main`**: Primary code branch that mirrors the upstream repository
- The workflow in `gh-actions` automatically syncs changes from upstream to `main`

### 2. Sync Workflow

The `sync-upstream.yaml` workflow:
- **Runs automatically** every day at 2:00 AM UTC
- **Can be triggered manually** from the Actions tab
- **Syncs changes** from `Mintimate/oh-my-rime` (upstream) to this fork's `main` branch
- **Uses** the `aormsby/Fork-Sync-With-Upstream-action@v3.4.1` action

### 3. Workflow Configuration

```yaml
name: 'Upstream Sync'

on:
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM UTC
  workflow_dispatch:     # Manual trigger
    inputs:
      sync_test_mode:
        description: 'Fork Sync Test Mode'
        type: boolean
        default: false
```

Key features:
- **Automatic scheduling**: Uses cron syntax for daily execution
- **Manual trigger**: Includes `workflow_dispatch` for on-demand syncing
- **Test mode**: Optional test mode for debugging without making changes
- **Permissions**: Requires `contents: write` to update the repository

## Setup Instructions

If you want to replicate this setup for your own fork:

### Step 1: Create the gh-actions branch
```bash
git checkout --orphan gh-actions
git rm -rf .
```

### Step 2: Add the workflow file
Create `.github/workflows/sync-upstream.yaml` with the configuration above, updating:
- `upstream_sync_repo`: Set to your upstream repository

### Step 3: Commit and push
```bash
git add .
git commit -m "Add upstream sync workflow"
git push -u origin gh-actions
```

### Step 4: Set as default branch
1. Go to Settings → Branches in your GitHub repository
2. Change the default branch from `main` to `gh-actions`
3. Confirm the change

### Step 5: Verify
1. Navigate to the Actions tab in your repository
2. You should see "Upstream Sync" in the workflows list
3. Click "Run workflow" to test manual triggering

## Maintenance

### Checking Sync Status
- Visit the [Actions tab](../../actions) to see workflow runs
- Each run shows whether new commits were found and synced
- Failed runs indicate potential merge conflicts that need manual resolution

### Modifying Sync Schedule
Edit the cron expression in `sync-upstream.yaml`:
- `'0 2 * * *'` - Daily at 2 AM UTC
- `'0 */6 * * *'` - Every 6 hours
- `'0 0 * * 0'` - Weekly on Sunday at midnight

### Handling Conflicts
If the automatic sync fails due to conflicts:
1. Switch to the `main` branch
2. Manually merge or rebase with upstream
3. Push the resolved changes
4. The next automatic sync will continue normally

## Benefits

1. **Automatic Updates**: Stay synchronized with upstream without manual intervention
2. **Clean Separation**: GitHub Actions configuration is isolated from code
3. **Full Visibility**: Workflows appear in the Actions tab and can be triggered manually
4. **Minimal Footprint**: The gh-actions branch contains only essential files

## Troubleshooting

### Workflow Not Visible
- Ensure `gh-actions` is set as the default branch
- Check that the workflow file has correct YAML syntax

### Sync Failures
- Check for merge conflicts in the `main` branch
- Verify the upstream repository name is correct
- Ensure the GitHub token has appropriate permissions

### Manual Sync
To manually sync outside of the workflow:
```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

## References

- [Fork-Sync-With-Upstream-action](https://github.com/aormsby/Fork-Sync-With-Upstream-action)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Original Implementation Article](https://ronaldln.github.io/MyPamphlet-Blog/2025/03/31/github-actionfork/)

---

*This branch is maintained automatically. For code contributions, please use the `main` branch.*