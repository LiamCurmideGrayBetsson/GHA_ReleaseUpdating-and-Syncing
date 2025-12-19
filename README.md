# GHA_ReleaseUpdating-and-Syncing

Automated GitHub Action workflow for updating release branches when PRs are merged to master.

## Overview

This repository contains a GitHub Action that automatically updates all active release branches with the latest changes from master whenever a PR is merged. The action updates branches that have open or draft pull requests, either targeting master/main directly or being targeted by feature branches. It also automatically detects and notifies developers about merge conflicts.

## How It Works

1. **Trigger**: The workflow activates when a PR is closed and merged into `master` or `main` branch
2. **Discovery**: It identifies release branches in two ways:
   - Branches with open or draft PRs **targeting master/main**
   - Release branches that have feature PRs **targeting them**
3. **Filter**: It filters for release branches (branches matching patterns like `release/*`, `release-*`, or version numbers like `1.0.0`)
4. **Update**: For each release branch found, it:
   - Checks out the branch
   - Merges the latest changes from master
   - Pushes the updates back to the remote
5. **Conflict Detection**: After updating, it checks all feature PRs targeting the release branches for conflicts
6. **Notification**: If conflicts are detected, it automatically posts a comment on the affected PR with:
   - Clear notification about the conflict
   - Step-by-step resolution instructions
   - Mention to the PR author
7. **Conflict Handling**: If merge conflicts occur during the release branch update itself, the workflow logs the issue and requires manual resolution

## Key Features

- ✅ **Auto-sync release branches** with master when PRs merge
- ✅ **Detects release branches** with both direct PRs to master and feature PRs to them
- ✅ **Automatic conflict detection** for feature branch PRs after updates
- ✅ **Smart notifications** that avoid duplicate comments on the same PR
- ✅ **Only updates active branches** with open or draft PRs

## Release Branch Patterns

The workflow recognizes the following patterns as release branches:
- `release/*` (e.g., `release/v1.0`, `release/2024-Q4`)
- `release-*` (e.g., `release-1.0`, `release-hotfix`)
- Version numbers (e.g., `1.0.0`, `2.1.0`)

## Setup

### Prerequisites

- The workflow uses the default `GITHUB_TOKEN` which has automatic permissions
- No additional secrets are required

### Installation

1. Copy the `.github/workflows/update-release-branches.yml` file to your repository
2. The workflow will automatically activate on your next PR merge to master

### Permissions

The workflow requires the following permissions (automatically granted via `GITHUB_TOKEN`):
- `contents: write` - To push updates to release branches
- `pull-requests: write` - To comment on PRs with conflict notifications

## Usage Examples

### Example 1: Release branch with PR to master

1. You have a release branch `release/v1.0` with an open PR **targeting master**
2. You merge a bug fix PR into `master`
3. The workflow automatically:
   - Detects that `release/v1.0` has an open PR targeting master
   - Merges master into `release/v1.0`
   - Pushes the updated branch

### Example 2: Feature branch with PR to release

1. You have `feature/login` with an open PR **targeting** `release/v1.0`
2. You merge a change into `master`
3. The workflow automatically:
   - Detects that `release/v1.0` has an open PR targeting it
   - Merges master into `release/v1.0`
   - Checks if `feature/login` PR now has conflicts
   - If conflicts exist, posts a comment on the feature PR notifying the author

### Example 3: Conflict notification

When conflicts are detected, the PR author receives a comment like:

```
🚨 **Merge Conflict Detected**

This PR now has conflicts with the base branch `release/v1.0`.

The release branch was recently updated with changes from `master`. 
Please resolve the conflicts to proceed with merging.

**Steps to resolve:**
1. Pull the latest changes from `release/v1.0`
2. Merge `release/v1.0` into your feature branch `feature/login`
3. Resolve any conflicts
4. Push the changes

cc @username
```

## Workflow Summary

After each run, the workflow provides a summary showing:
- Which base branch was merged into (master/main)
- The PR number that triggered the workflow
- Which release branches were processed
- Any branches that require manual conflict resolution
- Which PR authors were notified about conflicts

## Benefits

### For Development Teams

- **Early conflict detection**: Developers discover conflicts immediately when they occur, not at merge time
- **Reduced merge delays**: Teams can address conflicts proactively while working on features
- **Automatic synchronization**: Release branches stay up-to-date with master without manual intervention
- **Clear guidance**: Automated comments provide step-by-step instructions for conflict resolution

### For Release Management

- **Always up-to-date releases**: Release branches automatically receive the latest master changes
- **Visibility**: Easy to see which branches are affected by recent changes
- **Reduced merge debt**: Conflicts are addressed incrementally rather than accumulating

## Customization

You can customize the workflow by:
- Modifying the release branch pattern matching logic in the workflow file
- Adjusting which branches trigger the workflow (currently `master` and `main`)
- Customizing the conflict notification message
- Adding additional notification channels (Slack, Teams, etc.)

## Troubleshooting

### Merge Conflicts in Release Branch

If a merge conflict occurs when updating the release branch itself:
1. Check the workflow run summary for details
2. Manually resolve the conflict in the affected release branch
3. The branch will be updated on the next successful merge to master

### Branch Not Updated

Verify that:
- The branch has an open or draft PR (either to master/main OR from a feature branch to the release branch)
- The branch name matches a recognized release branch pattern
- The workflow has the necessary permissions

### No Conflict Notification

If you expect a conflict notification but didn't receive one:
- GitHub may take a few moments to detect merge conflicts after the branch is updated
- Check if a notification comment already exists (the workflow prevents duplicate notifications)
- Verify the PR is in an "open" state (not draft or closed)

### Developers Still Getting Duplicate Notifications

The workflow checks for existing conflict comments and only posts once. If duplicates occur:
- Check that the comment body contains "Merge Conflict Detected" exactly
- Verify the workflow has the latest version with duplicate prevention logic