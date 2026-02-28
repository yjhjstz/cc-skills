---
name: gitlab-ci
description: Use this skill when the user asks to see CI pipeline status, list recent
  pipelines, check build history, or view pipeline results. Triggers on phrases like
  "pipeline list", "CI status", "recent builds", "pipeline history", "show pipelines",
  "what pipelines ran".
allowed-tools: Bash
---

View GitLab CI/CD pipeline list for the current repository.

Extract from the user's message: an optional branch name or status filter (failed, success, running, pending, canceled). If not clearly provided, proceed without a filter.

Use `glab` CLI. Must run from a git directory with GitLab remote.

## Steps

1. Run `glab ci list -P 10` with optional filters:
   - If the user mentioned a known status (`failed`, `success`, `running`, `pending`, `canceled`, `skipped`, `created`, `manual`), add `-s <status>`
   - If the user mentioned a branch name, add `-r <branch>`
   - No filter → list all recent pipelines

2. Present results in a clean table showing: IID, Status, Ref, Created time, Duration.

3. Suggest follow-up actions:
   - `/gitlab-ci-log <iid>` for failed pipelines
   - `/gitlab-ci-run` to trigger a new run

IMPORTANT: Do NOT use interactive/TUI commands (`glab ci view`, `glab ci trace`).
