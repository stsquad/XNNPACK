---
name: check-ci-status
description: Query GitHub CI workflow status for all git remotes. Use when asked about CI status, build status, workflow runs, or GitHub Actions.
---

# Check CI Status Skill

This skill queries the GitHub CI status for all configured git remotes in the repository.

## Instructions

When asked about CI status, follow these steps:

### 1. Identify Git Remotes

First, list all configured git remotes:

```bash
git remote -v
```

### 2. Extract GitHub Repository Info

For each remote URL, extract the owner and repository name:
- SSH format: `git@github.com:OWNER/REPO.git`
- HTTPS format: `https://github.com/OWNER/REPO.git`

### 3. Query Workflow Runs

For each GitHub remote, use `gh` CLI to query recent workflow runs:

```bash
# List recent workflow runs for a specific repo
gh run list --repo OWNER/REPO --limit 10

# Get detailed status of a specific run
gh run view RUN_ID --repo OWNER/REPO

# List workflows available in the repo
gh workflow list --repo OWNER/REPO
```

### 4. Check Branch-Specific Status

To check CI status for the current branch:

```bash
# Get current branch name
git branch --show-current

# Check status for current branch
gh run list --repo OWNER/REPO --branch BRANCH_NAME --limit 5
```

### 5. Report Format

Present the status in a clear format:

```
## CI Status Report

### Remote: origin (github.com/google/XNNPACK)
| Workflow | Branch | Status | Conclusion | Date |
|----------|--------|--------|------------|------|
| Build    | main   | completed | success | 2024-01-15 |
| Tests    | main   | completed | failure | 2024-01-15 |

### Remote: github (github.com/stsquad/XNNPACK)
...
```

### 6. Highlight Issues

- Flag any failed or cancelled runs
- Note any workflows currently in progress
- Identify stale runs (no recent activity)

## Common Commands Reference

```bash
# View all workflow runs
gh run list --repo OWNER/REPO

# View failed runs only
gh run list --repo OWNER/REPO --status failure

# View in-progress runs
gh run list --repo OWNER/REPO --status in_progress

# Get workflow run logs
gh run view RUN_ID --repo OWNER/REPO --log

# Re-run failed jobs
gh run rerun RUN_ID --repo OWNER/REPO --failed

# View PR checks
gh pr checks PR_NUMBER --repo OWNER/REPO
```

## Error Handling

- If `gh` is not authenticated for a remote, inform the user to run `gh auth login`
- If a remote is not a GitHub URL, skip it and note that in the report
- If API rate limits are hit, suggest waiting or using a token with higher limits
