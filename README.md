# Jens' Claude Code Plugin Marketplace

A collection of Claude Code plugins designed to enhance your development workflow. Created to make my life easier
when switching between devices.

## Installation

To add this marketplace to your Claude Code installation, run:

```bash
/plugin marketplace add JensAstrup/jens-marketplace
```

Once added, you can install individual plugins from this marketplace using the standard plugin installation commands.

## Available Plugins

### lint-changes

Automatically run linters on all staged and unstaged changes, and fix warnings and errors across your codebase.

**Command:** `/lint-changes`

**What it does:**
- Runs your project's linter (e.g., `yarn lint --fix`) on all staged and unstaged changes
- Automatically fixes what can be auto-corrected
- Spawns parallel agents to fix remaining issues file-by-file
- Verifies all fixes and provides a comprehensive summary

**Workflow:**
1. Identifies all changed files using git status and diff
2. Runs the linter with auto-fix enabled
3. Spawns specialized agents for each file with remaining issues
4. Each agent persistently works to resolve all warnings and errors
5. Performs final verification and reports results

**Key Features:**
- Focuses only on your changed files, not the entire codebase
- Parallel processing for faster results
- Persistent agents that try multiple approaches to fix stubborn issues
- Does not stage changes automatically - leaves that control to you
- Comprehensive reporting of all fixes applied

**Common issues resolved:**
- Unused variables and imports
- Missing dependencies in React hooks
- Type errors
- Formatting inconsistencies
- Missing semicolons or trailing commas
- Inconsistent quote styles

---

### write-tests

Write or update tests for your code changes with intelligent scope detection.

**Commands:**
- `/write-tests changes` - Write tests for staged and unstaged changes only
- `/write-tests branch` - Write tests for all changes on the current branch (compared to default branch)

**What it does:**
- Analyzes your code changes and identifies files that need test coverage
- Creates new test files or updates existing ones following your project's conventions
- Spawns specialized test-writer agents to work in parallel
- Runs the test suite to verify all tests pass and that full coverage is achieved

**Usage:**

**For staged/unstaged changes:**
```bash
/write-tests changes
```
This workflow:
1. Examines git status and diffs to identify changed files
2. Determines which changes need test coverage
3. Updates existing tests or creates new ones for modified functionality
4. Focuses on testing the actual changes, not the entire file

**For branch changes:**
```bash
/write-tests branch
```
This workflow:
1. Compares the current branch against develop
2. Identifies all files changed on the branch
3. Ensures comprehensive test coverage for all branch modifications
4. Useful for pre-PR test coverage verification

**Key Features:**
- Two flexible scopes: granular (changes) or comprehensive (branch)
- Follows your project's existing test patterns and conventions
- Parallel test file processing for efficiency
- Automatic test suite execution to verify correctness
- Respects testing guidelines from CLAUDE.md if present
- One agent per test file for focused, quality test writing

**When to use which command:**
- Use `/write-tests changes` during active development to test as you go
- Use `/write-tests branch` before creating a PR to ensure complete coverage

---

## Contributing

Have ideas for new plugins or improvements? Contributions are welcome! Please feel free to submit issues or pull requests.
