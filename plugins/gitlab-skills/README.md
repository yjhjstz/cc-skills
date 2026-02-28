# gitlab-skills

GitLab workflow commands for Claude Code, powered by the [`glab`](https://gitlab.com/gitlab-org/cli) CLI.

## Prerequisites

- `glab` installed (e.g. `brew install glab`)
- Authenticated: `glab auth login`
- Run from a git directory with a GitLab remote

## Commands

| Command | Arguments | Description |
|---|---|---|
| `/gitlab-ci` | `[branch \| status]` | List recent CI/CD pipelines |
| `/gitlab-ci-log` | `<pipeline-iid>` | Show failed job logs and analyze errors |
| `/gitlab-ci-run` | `[branch]` | Trigger a new pipeline run |
| `/gitlab-mr-list` | `[@me \| closed \| label:<n> \| ...]` | List merge requests with filters |
| `/gitlab-mr-comments` | `<mr-iid>` | View and summarize MR discussions |
| `/gitlab-review-mr` | `<mr-iid>` | Full code review of a merge request |
| `/gitlab-fix-issue` | `<issue-number>` | Read issue, implement fix, open MR |

## Install via Plugin Marketplace

```
/plugin marketplace add https://github.com/yjhjstz/cc-skills
/plugin install gitlab-skills@cc-skills
```
