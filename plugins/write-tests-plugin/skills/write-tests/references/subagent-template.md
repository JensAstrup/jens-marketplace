# Test Generation Subagent Template

Use this template when launching subagents for test generation. Provide these instructions to each subagent along with the specific context for their test file.

## Your Task

You are responsible for creating or updating tests for a specific test file to ensure comprehensive coverage of the associated source code changes.

## What You'll Receive

- **Source file path**: The file that was modified
- **Source file changes**: The specific code changes made (git diff output)
- **Test file path**: Where the test file exists or should be created
- **Existing test file**: The current test contents (if the file exists)
- **Test framework**: The testing framework and assertion library in use
- **Test command**: The specific command to run your test file

## Your Responsibilities

### 1. Create or Update Tests

Write comprehensive tests that cover:
- **All code paths**: Every conditional branch, loop, and error handler
- **Positive cases**: Expected behavior with valid inputs
- **Negative cases**: Proper handling of invalid inputs
- **Edge cases**: Boundary conditions, empty inputs, null values, extreme values
- **Error conditions**: Exception handling, error messages, failure scenarios
- **Mock external dependencies**: API calls, database queries, file I/O, etc.

Follow these quality guidelines:
- Use clear, descriptive test names that explain what is being tested
- Each test should verify one specific behavior
- Use descriptive assertion messages
- Follow the existing test patterns and conventions in the codebase
- Ensure tests are isolated and don't depend on execution order

### 2. Run Tests for Your File

After writing or updating tests:
1. Run the test command for your specific test file with coverage (e.g., `pytest path/to/test_file.py --cov=path/to/source --cov-report=term-missing`, `yarn test -- path/to/test.js --coverage`)
2. Capture the test output and results
3. Check if all tests pass
4. Check that code has full coverage

### 3. Fix Test Failures Iteratively

If tests fail:
1. **Analyze the failure**: Read the error messages and stack traces carefully
2. **Identify the root cause**: Determine if the issue is with:
   - The test logic itself
   - Incorrect assertions or expectations
   - Missing mocks or fixtures
   - Setup or teardown problems
   - The source code (rare, but possible)
3. **Fix the issue**: Update the test code to address the root cause
4. **Re-run tests**: Execute the test command again
5. **Repeat**: Continue this process up to 5 times
6. **NEVER change the source code to fix test failures**

**Important**: Track your iteration count. After 5 attempts, stop and report back.

### 4. Improve Coverage Iteratively

If tests pass but coverage is incomplete:
1. **Review the source code**: Identify branches, edge cases, or code paths that the coverage report marks as missing
2. **Update the tests**: Add or adjust tests to cover the missing code
3. **Re-run tests with coverage**: Execute the test command with coverage again
4. **Repeat**: Continue this process no more than 5 times
5. **If coverage is still missing after 5 attempts**: Stop and return results to the main agent using the same reporting template as in step 5 (Report Results)—include what coverage was achieved and what remains uncovered

**Important**: Track your iteration count. After 5 attempts, stop and report back.

### 5. Report Results

**If all tests pass:**
Return a success message with:
- Test file path
- Number of tests created/updated
- Confirmation that all tests pass

**If tests still fail after 5 attempts:**
Return a failure report with:
- Test file path
- The most recent test failure output
- Your analysis of why the tests are failing
- Suggestions for manual intervention

## Example Subagent Workflow

```markdown
**Task**: Create tests for `src/auth/login.py`

**Changes**: Added email validation and rate limiting to login endpoint

**Test file**: `tests/test_auth_login.py`

**Test command**: `pytest tests/test_auth_login.py -v`

**Action Plan**:
1. Read existing test file (if exists)
2. Identify gaps in coverage for new email validation logic
3. Identify gaps in coverage for new rate limiting logic
4. Write tests for:
   - Valid email formats (positive cases)
   - Invalid email formats (negative cases)
   - Edge cases (empty string, very long emails, special characters)
   - Rate limiting enforcement
   - Rate limit reset behavior
5. Run tests and iterate on failures
6. Report results to main agent

**Expected Output**:
- 8 new tests added covering all branches
- All tests passing
- 100% coverage of changed code
```

## Test Quality Checklist

Before reporting success, verify:
- [ ] All modified code paths are tested
- [ ] Both success and failure scenarios are covered
- [ ] Edge cases are tested
- [ ] External dependencies are mocked appropriately
- [ ] Tests follow existing naming and structure conventions
- [ ] All tests pass when run
- [ ] Tests are maintainable and clear

## Common Issues and Solutions

**Issue**: Tests pass individually but fail when run together
- **Solution**: Check for shared state, global variables, or test interdependencies

**Issue**: Mocks not working as expected
- **Solution**: Verify mock paths match the import paths in the source code

**Issue**: Async tests timing out
- **Solution**: Check for missing `await` keywords, increase timeouts if needed

**Issue**: Tests fail on assertions with unclear error messages
- **Solution**: Add custom assertion messages to clarify what was expected

**Issue**: Cannot reproduce failure locally
- **Solution**: Check for environment-specific issues (env vars, file paths, dependencies)

## Reporting Template

**Success Report**:
```
✅ Tests completed successfully for [test_file_path]

- Created/Updated: [X] tests
- All tests passing
- Coverage: [describe what's covered]
```

**Failure Report**:
```
❌ Unable to fix test failures for [test_file_path] after 5 attempts

Last failure output:
[paste test output]

Analysis:
[explain what you tried and why it's still failing]

Recommendations:
[suggest what the user should check or fix manually]
```
