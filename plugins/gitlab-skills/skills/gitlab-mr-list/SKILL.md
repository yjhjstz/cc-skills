---
name: gitlab-mr-list
description: Use this skill when the user wants to list or browse GitLab merge requests.
  Triggers on phrases like "list MRs", "show merge requests", "open MRs", "my MRs",
  "what MRs are there", "show me the merge requests", "pending MRs".
allowed-tools: Bash
---

List GitLab merge requests for the current repository.

Extract from the user's message any filter hints: author (@me), status (closed, merged, draft), label, branch, or search text. If not provided, list all open MRs by default.

Use `glab` CLI. Must run from a git directory with GitLab remote.

## Steps

1. Build `glab mr list -P 15` with filters based on what the user mentioned:
   - "my MRs" / "mine" → add `--author=@me`
   - "review" / "assigned to me" → add `--reviewer=@me`
   - "closed" → add `-c`
   - "merged" → add `-M`
   - "draft" / "WIP" → add `-d`
   - label mentioned → add `-l <name>`
   - branch mentioned → add `-t <name>` (target branch)
   - other search text → add `--search "<text>"`
   - No filter → list open MRs (default)

2. Present results in a clean table: MR IID, Title, Author, Source→Target branch, Labels, Updated time.

3. Suggest follow-up actions:
   - `/gitlab-review-mr <iid>` to review a specific MR
   - `/gitlab-ci-log` if a MR's pipeline failed

IMPORTANT: Do NOT use interactive/TUI commands.
