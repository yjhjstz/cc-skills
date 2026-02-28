---
description: Find and fix a GitLab issue by reading it, implementing a fix, and creating a merge request
argument-hint: <issue-number>
allowed-tools: Bash, Read, Grep, Glob, Edit, Write
---

Find and fix GitLab issue #$ARGUMENTS. Must run from a git directory with GitLab remote.

Use `glab` CLI and `jq`.

## Steps

1. **Read the issue**:
   ```bash
   glab issue view $ARGUMENTS -F json
   ```
   Understand the problem described in the title, description, and labels. If unclear, also check comments with `glab issue view $ARGUMENTS -c`.

2. **Create a feature branch** from the default branch:
   ```bash
   git checkout -b fix/$ARGUMENTS
   ```

3. **Locate relevant code**: Search the codebase to understand the root cause. Use Grep, Glob, and Read tools.

4. **Implement the fix**: Make minimal, focused changes that address the root cause. Add appropriate tests if applicable.

5. **Commit and push**:
   ```bash
   git add <files> && git commit -m "Fix #$ARGUMENTS: <concise summary>"
   git push -u origin fix/$ARGUMENTS
   ```

6. **Create a merge request** linked to the issue:
   ```bash
   glab mr create -i $ARGUMENTS --fill --remove-source-branch --push
   ```
   If `--fill` doesn't produce a good description, use `-d` to provide one instead.

7. **Report**: Show the MR URL and a summary of changes made.

IMPORTANT:
- Confirm with the user before pushing and creating the MR.
- Do NOT use interactive/TUI commands.
