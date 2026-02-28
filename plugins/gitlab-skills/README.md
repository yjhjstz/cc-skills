# gitlab-skills

GitLab workflow commands for Claude Code, powered by the [`glab`](https://gitlab.com/gitlab-org/cli) CLI.

## Prerequisites

**Install glab:**

```bash
# macOS
brew install glab

# Linux
sudo apt install glab       # Debian/Ubuntu
# or download from https://gitlab.com/gitlab-org/cli/-/releases
```

**Authenticate** (replace `<your-token>` with a GitLab personal access token):

```bash
# Self-hosted GitLab
glab auth login --hostname <your-gitlab-host> --token <your-token>

# gitlab.com
glab auth login --token <your-token>
```

Token needs at least `api` scope. Generate at:
`https://<your-gitlab-host>/-/user_settings/personal_access_tokens`

**Proxy settings** — if you use an HTTP proxy, add your internal GitLab host to `NO_PROXY` so it is accessed directly:

```bash
export HTTP_PROXY="http://127.0.0.1:1080"
export HTTPS_PROXY="http://127.0.0.1:1080"
export NO_PROXY="<your-gitlab-host>,localhost,127.0.0.1"
```

Add these lines to `~/.bashrc` or `~/.zshrc` to make them permanent.

**Run from** a git directory with a GitLab remote.

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
