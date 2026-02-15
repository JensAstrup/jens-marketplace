---
name: write-tests
description: Create or update tests to reflect code changes. Use when the user wants to generate/write tests for their changes (staged/unstaged) or for an entire feature branch, ensure test coverage, or update existing tests to match code modifications.
argument-hint: "[changes|branch]"
allowed-tools: Bash(yarn test *)
---

# Write Tests

Automatically generate or update tests for code changes, ensuring comprehensive test coverage across all branches of logic.

## Argument Handling

Check `$ARGUMENTS[0]` to determine the scope:

- **`changes`**: Generate tests for all staged and unstaged changes in the working directory
- **`branch`**: Generate tests for all changes on the current branch compared to the default branch

If `$ARGUMENTS[0]` is not provided or is neither `changes` nor `branch`, respond with:

> "Please specify the scope for test generation:
> - `/write-tests changes` - Generate tests for staged and unstaged changes
> - `/write-tests branch` - Generate tests for all changes on the current branch
>
> Which would you like?"

## Workflow

### 1. Determine Scope and Analyze Changes

#### If `$ARGUMENTS[0]` == `changes`:

Use git commands to identify all modified files:
- Run `git diff --name-status HEAD` to see the names and status of all staged and unstaged changed files
- Identify which files have been added, modified, or deleted
- Determine the programming languages and frameworks in use

#### If `$ARGUMENTS[0]` == `branch`:

First, identify the branch context:
- Determine the current branch name using `git branch --show-current`
- Identify the default branch using `git symbolic-ref refs/remotes/origin/HEAD`
- Verify that the current branch is not the default branch (if it is, inform the user and exit gracefully)

Then, use git commands to identify all changes on the branch:
- Run `git diff --name-status <default-branch>...HEAD` to see the names and status of all files changed by this branch
- Identify which files have been added, modified, or deleted since branching
- Determine the programming languages and frameworks in use

### 2. Identify Test Agent Requirements

Based on the file changes:
- Check available agents to find specialized test agents (e.g., "backend test agent" for Python, "frontend test agent" for TypeScript/React)
- If a specialized test agent exists for the language/framework, note it for later use
- Group files by test agent type (backend, frontend, etc.)

### 3. Determine Test Files

For each modified file:
- Identify the corresponding test file (or where it should be created)
- Check if the test file exists
- Determine if tests need to be created or updated
- Group test files by the agent type they require

### 4. Launch Subagents for Test Generation

For each test file that needs to be created or updated:
- Launch a specialized subagent (Task tool) if an appropriate test agent is available, otherwise use a general-purpose agent
- Use the instructions from `references/subagent-template.md` for each subagent
- Provide the subagent with the source file changes, test file location, test framework, and test command

**IMPORTANT**: Launch all subagents in parallel by making multiple Task tool calls in a single message. Do not wait for one to complete before launching the next.

### 5. Monitor Subagent Completion

- Wait for all subagents to complete their work
- Review the changes made by each subagent
- Check if any subagents reported test failures they couldn't fix after 5 attempts
- Note which test files (if any) still have failing tests

### 6. Run Full Test Suite

Once all subagents have finished:
- Identify the project's test command (check package.json, pytest, etc.)
- Run the complete test suite for the project
- Capture the results

### 7. Report Results

Provide a summary:
- Scope used (changes or branch)
- For branch scope: branch name and comparison base
- Number of files changed
- Number of test files created
- Number of test files updated
- Final test suite status (pass/fail)
- **If any test files have persistent failures**:
  - List each test file that still has failures
  - Include the failure messages from the subagent
  - Explain that the subagent attempted to fix the failures 5 times but was unsuccessful
  - Suggest the user manually review these test files

## Quality Guidelines

### Test Coverage
- Tests must cover all code paths, including conditionals, loops, and error handlers
- Include positive cases, negative cases, edge cases, and error conditions
- Mock external dependencies appropriately
- Test both success and failure scenarios

### Test Quality
- Tests should be clear, focused, and well-named
- Each test should verify one specific behavior
- Use descriptive assertion messages
- Follow the existing test patterns in the codebase

### Subagent Instructions

All detailed instructions for subagents are in `references/subagent-template.md`. This includes:
- How to create comprehensive tests
- Running tests and fixing failures iteratively (up to 5 attempts)
- Reporting results back to the main agent

## Error Handling

- If `$ARGUMENTS[0]` is invalid or missing, prompt the user for the correct value
- If a subagent fails to complete, report the issue and continue with other agents
- If tests cannot be run (missing dependencies, etc.), report the issue clearly
- **If subagents report persistent failures, surface this information to the user with the test file names and failure details**
- If no changes are detected, inform the user and exit gracefully

## Example Usage

User: "/write-tests changes"
User: "/write-tests branch"
User: "Generate tests for my changes" → Should invoke with `changes` scope
User: "I need full test coverage before merging this PR" → Should invoke with `branch` scope
