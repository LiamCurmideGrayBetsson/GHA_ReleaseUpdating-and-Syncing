# Changelog

All notable changes to this project will be documented in this file.

## version1.0.1 - 2026-02-06

### Fixed
- Fixed subshell pipeline issue where step summary updates were not persisting
- Added handling for `UNKNOWN` and `null` mergeable states (GitHub often returns these immediately after base branch updates)
- Added git state cleanup between PR conflict checks to prevent cross-contamination
- Added comprehensive error handling for all `gh` CLI commands with graceful failure recovery
- Improved duplicate comment detection using HTML marker instead of text matching
- Enhanced logging with status indicators (✅/⚠️/ℹ️) for better visibility
- Only post conflict notifications when actual conflicts are detected, not just for unknown status

---

## version1.0.0 - 2026-01-14

- Initial tagged release of the reusable workflow.
- Added `on: workflow_call` support with inputs:
  - `base_branch` (required)
  - `merged_pr_number` (optional)
- Preserved `pull_request` trigger for merges into `main` or `master`.
- Centralized logic to:
  - Detect active release branches with open/draft PRs
  - Merge base branch updates into release branches
  - Detect PR merge conflicts and comment with resolution steps
- Updated documentation with reusable workflow consumer example and permissions guidance.
- Published tag `version1.0.0` for stable consumption from other repositories.

---

Notes:
- Consumers should pin to `@version1.0.0` (or a future stable tag) when referencing the workflow.
- Ensure the caller repository grants `contents: write` and `pull-requests: write` permissions to `GITHUB_TOKEN`.