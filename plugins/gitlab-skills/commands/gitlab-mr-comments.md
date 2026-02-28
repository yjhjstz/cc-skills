---
description: View and summarize all discussion threads on a GitLab merge request
argument-hint: <mr-iid>
allowed-tools: Bash
---

View comments and discussions on GitLab merge request $ARGUMENTS. Must run from a git directory with GitLab remote.

Argument: $ARGUMENTS (required - MR IID, e.g. `954`)

Use `glab` CLI and `jq`.

## Steps

1. **Get MR basic info**:
   ```bash
   glab mr view $ARGUMENTS -F json
   ```
   Extract: title, state, author, source/target branch.

2. **Get all discussion threads** — pipe directly to jq, no temp files needed:
   ```bash
   glab api "projects/:fullpath/merge_requests/$ARGUMENTS/discussions" --paginate | jq '<filter>'
   ```
   The `glab api` command handles authentication automatically. Pipe its output directly to `jq` for parsing.

3. **Parse and classify** with a single jq pipeline. Filter out system notes (where `notes[0].system` is true). Classify remaining into:
   - **Unresolved discussions** (threads where the discussion is not resolved)
   - **Code-level comments** (notes with `position.new_path` or `position.old_path`)
   - **General comments** (everything else)

   For complex jq filters, write the filter to a temp file and use `jq -f /tmp/filter.jq` to avoid shell quoting issues:
   ```bash
   # Write jq filter to file first, then:
   glab api "projects/:fullpath/merge_requests/$ARGUMENTS/discussions" --paginate | jq -f /tmp/filter.jq
   ```

4. **Present results** in this order:

   **MR Summary**: MR <IID> <title> — state, author, source->target

   **Unresolved Discussions** (show first, these need attention):
   For each unresolved thread:
   - If code-level: show `file:line` context, then the discussion thread (author, body, timestamp for each note)
   - If general: show the discussion thread

   **Resolved Discussions** (collapsed/summarized):
   - Count and brief summary (e.g., "12 resolved threads")
   - List one-line summaries: first note author + first ~80 chars of body

   **Statistics**:
   - Total discussions (excluding system notes)
   - Unresolved / Resolved count
   - Participants list

5. **Suggest follow-up actions**:
   - `/gitlab-review-mr $ARGUMENTS` — do a full code review
   - `glab mr note $ARGUMENTS -m "..."` — add a comment

IMPORTANT:
- Do NOT use interactive/TUI commands.
- Do NOT modify the MR (no approvals, no comments) unless user explicitly asks.
- If $ARGUMENTS is empty, tell the user to provide a MR IID.
- Shell quoting: For complex jq filters, ALWAYS write the jq filter to a temp file (e.g. /tmp/mr_filter.jq) and use `jq -f /tmp/mr_filter.jq` to avoid bash interpreting special characters. NEVER use the not-equal operator with exclamation mark inline — use `| not` instead in jq.
