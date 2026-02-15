---
name: lint-changes
description: >-
  Run linter on all staged and unstaged changes and fix all warnings/errors. Use when the user asks to lint their changes, run the linter and fix errors, fix lint issues in staged files, or check changes for lint warnings.
context: fork
---

# lint-changes

Run linter on all staged and unstaged changes and fix all warnings/errors

---

I need you to run the linter on all staged and unstaged changes in this repository and fix all warnings and errors.

Please follow these steps:

## Step 1: Run Lint with Auto-Fix
1. Run `yarn lint --fix` to automatically fix what can be fixed
2. The output will show any remaining files with warnings or errors
3. Parse the output to identify which files still have issues

## Step 2: Spawn Fix Agents
For each file with remaining warnings or errors:
1. Spawn ONE general-purpose agent per file
2. Pass the file path and specific lint errors/warnings to the agent
3. Instruct the agent to:
   - Read the file
   - Fix all lint warnings and errors
   - After editing, run `yarn lint <file-path>` to verify fixes
   - If issues remain, repeat the fix process until all issues are resolved
   - If stuck in a loop (same error 3+ times), report the issue back

Spawn all agents in parallel when possible.

## Step 3: Final Verification
After all agents complete:
1. Run `yarn lint` on the entire codebase to verify all issues are resolved
2. Report summary of:
   - Number of files that had issues
   - Number of files fixed
   - Any remaining issues that couldn't be auto-fixed
   - Files that were modified

## Important Notes
- Focus ONLY on staged and unstaged files (use `git status` and `git diff` to identify them)
- Do NOT stage any changes - leave that to the user
- Do NOT modify files that weren't in the original changeset
- Each agent should work independently on their assigned file
- Agents should be persistent - keep trying different approaches if initial fixes don't resolve all issues
- Common lint issues to watch for:
  - Unused variables/imports
  - Missing dependencies in useEffect/useCallback
  - Type errors
  - Formatting issues
  - Missing semicolons or trailing commas
  - Inconsistent quotes

## Example Agent Prompt
When spawning an agent for a file, use a prompt like:

```
Fix all ESLint warnings and errors in <file-path>

The following lint issues were found:
<paste specific errors/warnings>

Please:
1. Read the file
2. Fix all warnings and errors
3. Run `yarn lint <file-path>` to verify
4. If issues remain, fix them and verify again
5. Repeat until all issues are resolved or you've tried 3 times

If you cannot resolve an issue after 3 attempts, explain what you tried and why it couldn't be fixed.
```
