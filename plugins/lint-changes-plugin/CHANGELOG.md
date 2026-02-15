# Changelog

## 1.0.0 - 2026-02-15

### Added
- Initial release of lint-changes plugin
- Skill to run linter on all staged and unstaged changes and auto-fix warnings/errors
- Parallel subagent spawning for per-file lint fixes
- Iterative fix-and-verify loop (up to 3 attempts per file)
- Final verification step with summary report
