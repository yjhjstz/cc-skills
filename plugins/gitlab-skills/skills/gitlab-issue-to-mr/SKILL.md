---
name: gitlab-issue-to-mr
description: >-
  Use this skill when the user wants to fix a GitLab issue end-to-end:
  implement the fix, push, create an MR, watch CI, and auto-fix CI failures until
  the pipeline passes. Triggers on phrases like "fix issue and create mr", "implement
  and submit issue", "fix issue end to end", "fix issue with ci", "full issue workflow".
allowed-tools: Bash, Read, Grep, Glob, Edit, Write
---

Full workflow: fix a GitLab issue → create MR → monitor CI → auto-fix failures → repeat until green.

Extract the issue number from the user's message (e.g. "fix issue 42" or "#42" → 42). If not provided, ask before proceeding.

Use `glab` CLI and `jq`. Must run from a git directory with GitLab remote.

---

## Phase 1: Fix the Issue

1. **Read the issue**:
   ```bash
   glab issue view <ISSUE_NUMBER> -F json
   glab issue view <ISSUE_NUMBER> -c   # read comments if description is unclear
   ```

2. **Create a feature branch** from the default branch:

   Derive a short title from the issue title: lowercase, max 4 words, spaces replaced with `-`, special characters removed.
   Example: "Fix login timeout error" → `fix-login-timeout-error`

   ```bash
   git checkout <default_branch> && git pull
   git checkout -b claude/<ISSUE_NUMBER>-<short-title>
   ```
   Example: `claude/42-fix-login-timeout`

3. **Locate relevant code**: Use Grep, Glob, Read to understand root cause.

4. **Implement the fix**: Minimal, focused changes. Add tests if applicable.

5. **Commit and push** (confirm with user first):
   ```bash
   git add <files>
   git commit -m "Fix #<ISSUE_NUMBER>: <concise summary>"
   git push -u origin claude/<ISSUE_NUMBER>-<short-title>
   ```

---

## Phase 2: Create Merge Request

6. **Create MR** linked to the issue:
   ```bash
   glab mr create -i <ISSUE_NUMBER> --fill --remove-source-branch --push
   ```
   Capture and show the MR URL from the output.

---

## Phase 3: Monitor CI and Auto-fix Loop

Repeat the following loop (max **3 iterations** to avoid infinite loops):

### 3a. Wait for pipeline to start

```bash
# Poll every 15s until pipeline appears on the branch (max 2 min)
BRANCH="claude/<ISSUE_NUMBER>-<short-title>"
for i in $(seq 1 8); do
  PIPELINE=$(glab ci list -b "$BRANCH" -F json -P 5 | jq 'first(.[] | select(.status != "skipped"))')
  [ "$PIPELINE" != "null" ] && [ -n "$PIPELINE" ] && break
  echo "Waiting for pipeline... ($i/8)"; sleep 15
done
PIPELINE_IID=$(echo "$PIPELINE" | jq '.iid')
PIPELINE_ID=$(echo "$PIPELINE" | jq '.id')
echo "Pipeline IID: $PIPELINE_IID  ID: $PIPELINE_ID"
```

### 3b. Wait for pipeline to finish

```bash
# Poll every 60s until status is not running/pending/created (max 3 hours)
for i in $(seq 1 180); do
  STATUS=$(glab api "projects/:fullpath/pipelines/$PIPELINE_ID" | jq -r '.status')
  ELAPSED=$(( i * 60 ))
  echo "Status: $STATUS (${ELAPSED}s elapsed)"
  [[ "$STATUS" != "running" && "$STATUS" != "pending" && "$STATUS" != "created" ]] && break
  sleep 60
done
echo "Final status: $STATUS"
```

### 3c. If pipeline passed → done

```bash
if [ "$STATUS" = "success" ]; then
  echo "CI passed! MR is ready for review."
  # exit loop
fi
```

Report MR URL and summary. Stop here.

### 3d. If pipeline failed → analyze and fix

**Get failed jobs and logs**:
```bash
FAILED_JOBS=$(glab api "projects/:fullpath/pipelines/$PIPELINE_ID/jobs?per_page=100" \
  | jq '[.[] | select(.status == "failed") | {id, name, stage}]')
echo "$FAILED_JOBS"

# Fetch trace for each failed job (last 150 lines)
for JOB_ID in $(echo "$FAILED_JOBS" | jq '.[].id'); do
  echo "=== Job $JOB_ID ==="
  glab api "projects/:fullpath/jobs/$JOB_ID/trace" | tail -500
done
```

**Analyze** the error output and identify root cause.

**Implement fix**: Edit relevant files to resolve the CI failure.

**Commit and push**:
```bash
git add <changed_files>
git commit -m "Fix CI: <brief description of what was fixed>"
git push
```

Go back to **3a** for the next pipeline.

---

## Phase 4: Report

After the loop ends, report:
- MR URL
- Summary of all changes made (issue fix + any CI fixes)
- Final CI status
- If still failing after 3 iterations: show remaining errors and ask user how to proceed

---

## IMPORTANT Rules

- **Always confirm** with the user before the first push and MR creation (Phase 1 step 5).
- After MR creation, proceed with CI monitoring **without asking again** unless CI fails 3 times.
- Do NOT use interactive/TUI commands (`glab ci view`, `glab ci trace`).
- Use `:fullpath` in `glab api` URLs — auto-resolves from git remote.
- Use `jq` for all JSON parsing.
- Max **3 CI fix iterations** to prevent infinite loops; report to user if still failing.
- If CI fails with a flaky/infrastructure error (not a code issue), retry once before attempting a code fix.
