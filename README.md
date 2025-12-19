# GHA_ReleaseUpdating-and-Syncing

Automated GitHub Action workflow for updating release branches when PRs are merged to master.

## Overview

This repository contains a GitHub Action that automatically updates all active release branches with the latest changes from master whenever a PR is merged. The action only updates branches that have open or draft pull requests associated with them.

## How It Works

1. **Trigger**: The workflow activates when a PR is closed and merged into `master` or `main` branch
2. **Discovery**: It identifies all branches with open or draft PRs **that target master/main**
3. **Filter**: It filters for release branches (branches matching patterns like `release/*`, `release-*`, or version numbers like `1.0.0`)
4. **Update**: For each release branch found, it:
   - Checks out the branch
   - Merges the latest changes from master
   - Pushes the updates back to the remote
5. **Conflict Handling**: If merge conflicts occur, the workflow logs the issue and requires manual resolution

**Important**: Only release branches with active PRs **targeting master/main** will be updated. Feature branches pointing to release branches will NOT trigger release branch updates.

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
- `pull-requests: read` - To list open and draft PRs

## Usage Example

1. You have a release branch `release/v1.0` with an open PR **targeting master**
2. You merge a bug fix PR into `master`
3. The workflow automatically:
   - Detects that `release/v1.0` has an open PR targeting master
   - Merges master into `release/v1.0`
   - Pushes the updated branch

**Note**: If you have a feature branch with a PR targeting a release branch (e.g., `feature/login` → `release/v1.0`), that release branch will NOT be updated because the PR is not targeting master.

## Workflow Summary

After each run, the workflow provides a summary showing:
- Which base branch was merged into (master/main)
- The PR number that triggered the workflow
- Which release branches were processed
- Any branches that require manual conflict resolution

## Customization

You can customize the workflow by:
- Modifying the release branch pattern matching logic in the workflow file
- Adjusting which branches trigger the workflow (currently `master` and `main`)
- Adding notification steps for failures

## Troubleshooting

### Merge Conflicts

If a merge conflict occurs:
1. Check the workflow run summary for details
2. Manually resolve the conflict in the affected release branch
3. The branch will be updated on the next successful merge to master

### Branch Not Updated

Verify that:
- The branch has an open or draft PR **targeting master/main** (not targeting another release branch)
- The branch name matches a recognized release branch pattern
- The workflow has the necessary permissions
