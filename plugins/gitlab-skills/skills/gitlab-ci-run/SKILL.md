---
name: gitlab-ci-run
description: Use this skill when the user wants to trigger a new GitLab CI pipeline,
  run CI, start a build, or kick off a pipeline. Triggers on phrases like "trigger
  pipeline", "run CI", "start build", "kick off pipeline", "re-run CI", "run the
  pipeline".
allowed-tools: Bash
---

Trigger a new GitLab CI/CD pipeline run.

Extract the branch name from the user's message if provided. If not specified, default to the current branch.

Use `glab` CLI. Must run from a git directory with GitLab remote.

## Steps

1. **Determine branch**: If the user specified a branch name, use it. Otherwise use current branch (`git branch --show-current`).

2. **Confirm with user** before triggering: show branch name and ask for confirmation.

3. **Trigger**: `glab ci run -b <BRANCH>`

4. **Show result**: Display the new pipeline URL, suggest `/gitlab-ci` to monitor status.

IMPORTANT: Do NOT use interactive/TUI commands (`glab ci view`, `glab ci trace`).
