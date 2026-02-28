---
name: gitlab-fix-issue
description: Use this skill when the user wants to fix or resolve a GitLab issue.
  Triggers on phrases like "fix issue", "resolve GitLab issue", "work on issue",
  "implement fix for issue", "tackle issue", "address issue #".
allowed-tools: Bash, Read, Grep, Glob, Edit, Write
---

Find and fix a GitLab issue by reading it, implementing a fix, and creating a merge request.

Extract the issue number from the user's message (e.g. "fix issue 42" or "#42" → issue 42). If not provided, ask the user for the issue number before proceeding.

Use `glab` CLI and `jq`. Must run from a git directory with GitLab remote.

## Steps

1. **Read the issue**:
   ```bash
   glab issue view <ISSUE_NUMBER> -F json
   ```
   Understand the problem described in the title, description, and labels. If unclear, also check comments with `glab issue view <ISSUE_NUMBER> -c`.

2. **Create a feature branch** from the default branch:
   ```bash
   git checkout -b fix/<ISSUE_NUMBER>
   ```

3. **Locate relevant code**: Search the codebase to understand the root cause. Use Grep, Glob, and Read tools.

4. **Implement the fix**: Make minimal, focused changes that address the root cause. Add appropriate tests if applicable.

5. **Commit and push**:
   ```bash
   git add <files> && git commit -m "Fix #<ISSUE_NUMBER>: <concise summary>"
   git push -u origin fix/<ISSUE_NUMBER>
   ```

6. **Create a merge request** linked to the issue:
   ```bash
   glab mr create -i <ISSUE_NUMBER> --fill --remove-source-branch --push
   ```
   If `--fill` doesn't produce a good description, use `-d` to provide one instead.

7. **Report**: Show the MR URL and a summary of changes made.

IMPORTANT:
- Confirm with the user before pushing and creating the MR.
- Do NOT use interactive/TUI commands.
