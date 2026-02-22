---
name: address-comments
description: >-
  Resolve PR review comments by implementing requested changes. Use when the user asks to address PR comments, fix review feedback, resolve PR review comments, handle code review suggestions, or implement reviewer-requested changes. Examples: "Address the PR review comments", "Fix the review feedback on my PR", "Resolve the comments on my pull request", "Handle the code review suggestions".
allowed-tools: Bash(gh pr *), Bash(gh api *), Bash(git *)
---

# Address PR Comments

Retrieve all review comments on the current branch's pull request, present a summary of each requested change for user approval, then implement approved changes using subagents grouped by file.

Review the workflow steps below and create a to-do list to ensure each item is completed during execution

## Workflow

### 1. Identify the Pull Request

1. Get the open PR for the current branch
2. If no PR exists for this branch, inform the user and exit gracefully

### 2. Retrieve All Review Comments

Fetch all review comments on the PR:

1. Use `gh api repos/{owner}/{repo}/pulls/{number}/comments` to get inline review comments
2. Use `gh api repos/{owner}/{repo}/issues/{number}/comments` to get top-level PR comments
3. Filter out comments that are:
   - Already resolved
   - Pure questions with no actionable request
4. For each remaining comment, capture:
   - The comment ID
   - The file path and line number (for inline comments)
   - The comment body
   - The diff hunk context (for inline comments)

### 3. Summarize Changes for Approval

Present the user with a numbered list of changes to be made. Each item should include:

- **File path** (if applicable)
- **1-2 sentence description** of the change the reviewer is requesting
- The **comment author**

Format:

```
PR: <title> (#<number>)

Review comments to address:

1. `src/components/Button.tsx` - Rename the `handleClick` prop to `onClick` for consistency with React conventions. (reviewer: @username)
2. `src/utils/format.ts` - Add null check before calling `.toString()` to prevent runtime errors. (reviewer: @username)
3. General - Update the PR description to include a test plan. (reviewer: @username)
```

Ask the user which changes they want to address. They can:
- Approve all changes
- Specify individual numbers (e.g., "1, 3, 5")
- Reject all and exit

### 4. Implement Approved Changes

Group the approved changes by file path. For each file (or group of related files):

1. Launch a **general-purpose subagent** via the Task tool
2. Provide the subagent with:
   - The file path
   - The specific changes to make (comment body + diff context OR the portion of the comment delineated as being an AI prompt)
   - Instructions to read the file and make the changes
3. Instruct the subagent to:
   - Read the current file contents
   - Implement the requested change(s) for that file
   - Report back what was changed

For general comments not tied to a specific file, group them together and assign them to a single general-purpose subagent.

**IMPORTANT**: Launch all subagents in parallel by making multiple Task tool calls in a single message. Do not wait for one to complete before launching the next.

### 5. Resolve Comments

After each change is successfully implemented:

1. Use `gh api` to resolve the corresponding review threads
2. For inline review comments, use: `gh api --method PATCH repos/{owner}/{repo}/pulls/comments/{comment_id} -f body="..."` or mark the thread as resolved via the GraphQL API
3. Report which comments were resolved

### 6. Offer Test Generation

After all changes are implemented, ask the user:

> "Would you like to add tests for these changes?"

If the user says yes:
- Check if the `write-tests` skill is available by looking for it in the installed plugins/skills
- If available, execute `/write-tests changes` to generate tests for the implemented changes
- If the write-tests skill is not available, inform the user that the write-tests plugin is not installed and suggest they install it

### 7. Report Results

Provide a summary that includes:

- Number of comments addressed
- A list of files modified with a brief description of each change
- Number of comments resolved on the PR
- Any comments that could not be addressed (with explanation)

## Error Handling

- If no PR exists for the current branch, inform the user and exit
- If the PR has no review comments, inform the user and exit
- If a subagent fails to implement a change, report the failure and continue with remaining changes
- If comment resolution fails (permissions, API errors), report the issue but do not fail the entire workflow
- If the user rejects all changes, exit gracefully
