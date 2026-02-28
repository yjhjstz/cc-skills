---
description: Trigger a new GitLab CI/CD pipeline run
argument-hint: [branch-name]
allowed-tools: Bash
---

Trigger a new GitLab CI/CD pipeline run.

Argument: $ARGUMENTS (optional - branch name; defaults to current branch)

Use `glab` CLI. Must run from a git directory with GitLab remote.

## Steps

1. **Determine branch**: If `$ARGUMENTS` is provided and non-empty, use it. Otherwise use current branch (`git branch --show-current`).

2. **Confirm with user** before triggering: show branch name and ask for confirmation.

3. **Trigger**: `glab ci run -b <BRANCH>`

4. **Show result**: Display the new pipeline URL, suggest `/gitlab-ci` to monitor status.

IMPORTANT: Do NOT use interactive/TUI commands (`glab ci view`, `glab ci trace`).
