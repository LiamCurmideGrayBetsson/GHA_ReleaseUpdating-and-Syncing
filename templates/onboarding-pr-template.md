# PR: Add shared release updater workflow

## Summary
Adds a minimal caller workflow that reuses the centralized release updater from `LiamCurmideGrayBetsson/GHA_ReleaseUpdating-and-Syncing` pinned to `@version1.0.0`.

## Changes
- Adds `.github/workflows/use-shared-release-updater.yml` to call the shared workflow
- Grants required permissions (`contents: write`, `pull-requests: write`)

## Caller Workflow (copy into your repo)
```yaml
name: Use Shared Release Updater
on:
  pull_request:
    types: [closed]
    branches:
      - main
      - master

permissions:
  contents: write
  pull-requests: write

jobs:
  call-shared:
    if: github.event.pull_request.merged == true
    uses: LiamCurmideGrayBetsson/GHA_ReleaseUpdating-and-Syncing/.github/workflows/update-release-branches.yml@version1.0.0
    with:
      base_branch: ${{ github.event.pull_request.base.ref }}
      merged_pr_number: ${{ github.event.pull_request.number }}
    secrets: inherit
```

## Required settings
- Settings → Actions → General → Workflow permissions: set `GITHUB_TOKEN` to "Read and write permissions"
- Ensure this repository can read `LiamCurmideGrayBetsson/GHA_ReleaseUpdating-and-Syncing` (same org if private)

## Test plan
- Merge a small PR into `main`/`master`
- Confirm the job runs and logs updated release branches or a skip message
- If there are active release branches with open/draft PRs, verify they received updates

## Rollback
- Revert this PR to remove the caller workflow

## Checklist
- [ ] Caller workflow added under `.github/workflows/`
- [ ] Permissions set to Read and write
- [ ] Pinned to `@version1.0.0`
- [ ] Basic test PR merged and verified
