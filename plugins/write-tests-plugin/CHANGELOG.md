# Changelog

## 2.2.0 - 2026-02-15

### Changed

- Improved result summary to use a table format with per-file test counts and pass/fail status
- Updated failure messaging to reference subagents (plural) and suggest plan mode or agent teams for manual review

## 2.1.0 - 2026-02-15

### Changed

- Pre-fetch staged and unstaged changes as inline context instead of requiring a runtime git command
- `branch` scope now includes uncommitted changes in addition to committed branch changes

## 2.0.0 - 2026-02-15

### Changed

- Migrated from standalone skill to plugin format
- Enhanced test coverage instructions and subagent workflow
- Improved plugin coverage command reference
- Updated allowed commands to cover multiple options

### Added

- Support for `changes` and `branch` scopes
- Parallel subagent launching for test generation
- Iterative test fix loop (up to 5 attempts per file)
- Comprehensive result reporting with failure details

## 1.0.0 - 2026-02-15

### Added

- Initial release of write-tests skill
- Automatic test generation for staged/unstaged changes and feature branches
- Subagent-based test generation with specialized agent support
