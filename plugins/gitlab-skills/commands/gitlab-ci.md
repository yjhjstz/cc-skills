---
description: View GitLab CI/CD pipeline list for the current repository
argument-hint: [branch-name | failed | success | running | pending | canceled]
allowed-tools: Bash
---

View GitLab CI/CD pipeline list for the current repository.

Argument: $ARGUMENTS (optional - a branch name like `main`, or a status like `failed`, `success`, `running`, `pending`, `canceled`)

Use `glab` CLI. Must run from a git directory with GitLab remote.

## Steps

1. Run `glab ci list -P 10` with optional filters:
   - If `$ARGUMENTS` matches a known status (`failed`, `success`, `running`, `pending`, `canceled`, `skipped`, `created`, `manual`), add `-s $ARGUMENTS`
   - Otherwise treat it as a branch name and add `-r $ARGUMENTS`
   - No argument → list all recent pipelines

2. Present results in a clean table showing: IID, Status, Ref, Created time, Duration.

3. Suggest follow-up actions:
   - `/gitlab-ci-log <iid>` for failed pipelines
   - `/gitlab-ci-run` to trigger a new run

IMPORTANT: Do NOT use interactive/TUI commands (`glab ci view`, `glab ci trace`).
