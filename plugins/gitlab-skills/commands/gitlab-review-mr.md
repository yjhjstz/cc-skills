---
description: Do a thorough code review of a GitLab merge request covering correctness, security, performance, and test coverage
argument-hint: <mr-iid>
allowed-tools: Bash, Read, Grep, Glob
---

Review GitLab merge request !$ARGUMENTS. Must run from a git directory with GitLab remote.

Use `glab` CLI and `jq`.

## Steps

1. **Get MR details**:
   ```bash
   glab mr view $ARGUMENTS -F json
   ```
   Understand: title, description, source/target branch, labels, related issues.

2. **Get the diff**:
   ```bash
   glab mr diff $ARGUMENTS --color=never
   ```
   If the diff is very large, use `glab api "projects/:fullpath/merge_requests/$ARGUMENTS/changes"` to get structured per-file changes and review the most critical files first.

3. **Review the changes** thoroughly. For each changed file, evaluate:
   - **Correctness**: Logic errors, edge cases, off-by-one, null/error handling
   - **Security**: Injection, auth bypass, secrets exposure, OWASP top 10
   - **Performance**: N+1 queries, unnecessary allocations, missing indexes
   - **Concurrency**: Race conditions, deadlocks (especially important for MPP/database code)
   - **Tests**: Are changes covered? Are tests meaningful or just checking happy path?
   - **Style**: Consistency with surrounding code, naming conventions

4. **Check CI status** (if relevant):
   ```bash
   glab ci list -P 5 -r <source-branch>
   ```
   Note any failed pipelines — use `/gitlab-ci-log` if needed.

5. **Present the review** in a structured format:
   - **Summary**: What the MR does in 1-2 sentences
   - **Verdict**: APPROVE / REQUEST_CHANGES / COMMENT
   - **Findings**: Grouped by severity (critical > major > minor > nit)
   - Each finding: file, line, description, suggested fix if applicable

6. **Post the review** (only if the user confirms):
   - For APPROVE: `glab mr approve $ARGUMENTS`
   - For comments: `glab mr note $ARGUMENTS -m "<review summary>"`

IMPORTANT:
- Do NOT approve or comment without user confirmation.
- Do NOT use interactive/TUI commands.
- Read the actual source files (not just the diff) when you need more context to understand a change.
