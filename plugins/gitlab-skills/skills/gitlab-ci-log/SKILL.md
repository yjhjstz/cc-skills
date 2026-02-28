---
name: gitlab-ci-log
description: Use this skill when the user asks why a GitLab CI pipeline failed, wants
  to see job logs, or needs to debug a pipeline error. Triggers on phrases like "pipeline
  failed", "CI error", "job logs", "why did the build fail", "debug pipeline", "what
  went wrong in pipeline".
allowed-tools: Bash
---

View failed job logs for a GitLab CI/CD pipeline and analyze the failure.

Extract the pipeline IID from the user's message (e.g. "pipeline 6954" → IID 6954). If not provided, ask the user for the pipeline IID before proceeding.

Use `glab` CLI and `jq`. Must run from a git directory with GitLab remote.

## Steps

1. **Get pipeline ID from IID**: `glab ci list` shows IID, but the API needs the global pipeline ID. Use JSON output to get the mapping:
   ```bash
   PIPELINE_ID=$(glab ci list -F json -P 50 | jq '.[] | select(.iid == <IID>) | .id')
   ```

2. **Get failed jobs** using `glab api` with `:fullpath` (auto-resolves project path):
   ```bash
   glab api "projects/:fullpath/pipelines/$PIPELINE_ID/jobs?per_page=100" | jq '[.[] | select(.status == "failed")]'
   ```

3. **Fetch trace log** for each failed job:
   ```bash
   glab api "projects/:fullpath/jobs/<JOB_ID>/trace"
   ```
   Show only the last ~100 lines to focus on errors.

4. **Analyze**: Identify root cause, highlight key error messages, suggest fixes.

Chain steps 1-2 in a single Bash call. Process multiple failed jobs one by one, showing job name, stage, and error output.

IMPORTANT:
- Do NOT use `glab ci get` — it takes global pipeline ID, not IID.
- Do NOT use interactive/TUI commands (`glab ci view`, `glab ci trace`).
- Use `:fullpath` in `glab api` URLs — it auto-resolves from git remote. No manual path encoding.
- Use `jq` for JSON parsing.
