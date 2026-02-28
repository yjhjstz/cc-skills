# cc-skills

Custom [Claude Code](https://claude.ai/code) plugin marketplace with developer workflow skills.

## Installation

**Step 1 — Add this marketplace:**

```
/plugin marketplace add https://github.com/yjhjstz/cc-skills
```

**Step 2 — Install the plugin:**

```
/plugin install gitlab-skills@cc-skills
```

## Available Plugins

### `gitlab-skills`

GitLab workflow slash commands powered by the [`glab`](https://gitlab.com/gitlab-org/cli) CLI.

**Prerequisites:** `glab` installed and authenticated (`glab auth login`), run from a git repo with a GitLab remote.

| Command | Arguments | Description |
|---|---|---|
| `/gitlab-ci` | `[branch \| status]` | List recent CI/CD pipelines |
| `/gitlab-ci-log` | `<pipeline-iid>` | Show failed job logs and analyze errors |
| `/gitlab-ci-run` | `[branch]` | Trigger a new pipeline run |
| `/gitlab-mr-list` | `[@me \| closed \| label:<n> \| ...]` | List merge requests with filters |
| `/gitlab-mr-comments` | `<mr-iid>` | View and summarize MR discussions |
| `/gitlab-review-mr` | `<mr-iid>` | Full code review of a merge request |
| `/gitlab-fix-issue` | `<issue-number>` | Read issue, implement fix, open MR |

## Repository Structure

```
cc-skills/
├── .claude-plugin/
│   └── marketplace.json        # Marketplace manifest
├── plugins/
│   └── gitlab-skills/
│       ├── .claude-plugin/
│       │   └── plugin.json     # Plugin manifest
│       ├── README.md
│       └── commands/           # Slash commands
│           ├── gitlab-ci.md
│           ├── gitlab-ci-log.md
│           ├── gitlab-ci-run.md
│           ├── gitlab-fix-issue.md
│           ├── gitlab-mr-comments.md
│           ├── gitlab-mr-list.md
│           └── gitlab-review-mr.md
└── README.md
```
