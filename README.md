# cc-skills

Custom Claude Code plugin marketplace with developer workflow skills.

## Add This Marketplace

```
/plugin marketplace add https://github.com/yjhjstz/cc-skills
```

## Available Plugins

### `gitlab-skills`

GitLab CI/CD and MR workflow commands via the `glab` CLI.

```
/plugin install gitlab-skills@cc-skills
```

See [plugins/gitlab-skills/README.md](plugins/gitlab-skills/README.md) for full documentation.

## Structure

```
cc-skills/
├── .claude-plugin/
│   └── marketplace.json        # Marketplace manifest
├── plugins/
│   └── gitlab-skills/
│       ├── .claude-plugin/
│       │   └── plugin.json     # Plugin manifest
│       ├── README.md
│       └── commands/           # User-invocable slash commands
│           ├── gitlab-ci.md
│           ├── gitlab-ci-log.md
│           ├── gitlab-ci-run.md
│           ├── gitlab-fix-issue.md
│           ├── gitlab-mr-comments.md
│           ├── gitlab-mr-list.md
│           └── gitlab-review-mr.md
└── README.md
```
