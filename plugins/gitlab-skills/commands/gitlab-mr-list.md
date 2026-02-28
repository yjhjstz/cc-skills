---
description: List GitLab merge requests with optional filtering by author, status, label, or branch
argument-hint: [@me | closed | merged | draft | label:<name> | branch:<name> | <search-text>]
allowed-tools: Bash
---

List GitLab merge requests for the current repository.

Argument: $ARGUMENTS (optional - filter keyword. Examples: `@me`, `closed`, `merged`, `draft`, `label:bug`, `branch:main`, or free-text search)

Use `glab` CLI. Must run from a git directory with GitLab remote.

## Steps

1. Build `glab mr list -P 15` with filters based on `$ARGUMENTS`:
   - `@me` → add `--author=@me`
   - `review` or `review:@me` → add `--reviewer=@me`
   - `closed` → add `-c`
   - `merged` → add `-M`
   - `draft` → add `-d`
   - `label:<name>` → add `-l <name>`
   - `branch:<name>` → add `-t <name>` (target branch)
   - Any other text → add `--search "$ARGUMENTS"`
   - No argument → list open MRs (default)

2. Present results in a clean table: MR IID, Title, Author, Source→Target branch, Labels, Updated time.

3. Suggest follow-up actions:
   - `/gitlab-review-mr <iid>` to review a specific MR
   - `/gitlab-ci-log` if a MR's pipeline failed

IMPORTANT: Do NOT use interactive/TUI commands.
