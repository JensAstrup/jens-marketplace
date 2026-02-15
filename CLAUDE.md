# CLAUDE.md

## Project Overview

This is a Claude Code plugin marketplace - a collection of plugins distributed via a single marketplace repository. It contains no application code; only Markdown skill definitions and JSON metadata.

## Repository Structure

- `.claude-plugin/marketplace.json` - Marketplace manifest listing all plugins with versions, descriptions, and source paths
- `plugins/<plugin-name>/` - Each plugin directory contains:
  - `skills/<skill-name>/SKILL.md` - Skill definition (YAML frontmatter + Markdown instructions)
  - `skills/<skill-name>/references/` - Supporting documents referenced by the skill (e.g., subagent templates)
  - `CHANGELOG.md` - Plugin-level changelog

See "Plugins reference" in Documentation section for full structure (including commands, hooks, and agents)

## Plugin Versioning

Plugin versions are tracked in two places that must stay in sync:
1. `.claude-plugin/marketplace.json` - the `version` field for each plugin entry
2. `plugins/<plugin-name>/CHANGELOG.md` - the latest version heading

## Skills

### Skill File Format

SKILL.md files use YAML frontmatter between `---` markers, followed by Markdown instructions. All fields are optional; only `description` is required.

| Field | Description |
|---|---|
| `name` | Display name / slash-command name. Lowercase letters, numbers, hyphens only (max 64 chars). Defaults to directory name. |
| `description` | What the skill does and when to use it. Claude uses this to decide when to load the skill automatically so provide examples of user input that should trigger this skill. |
| `argument-hint` | Hint shown during autocomplete (e.g., `[changes\|branch]`, `[filename] [format]`). |
| `disable-model-invocation` | Set `true` to prevent Claude from automatically loading this skill. User must invoke manually via `/name`. Default: `false`. |
| `user-invocable` | Set `false` to hide from the `/` menu. For background knowledge only. Default: `true`. |
| `allowed-tools` | Tools Claude can use without asking permission when this skill is active (e.g., `Read, Grep, Glob` or `Bash(yarn test *)`). |
| `model` | Model to use when this skill is active. |
| `context` | Set to `fork` to run in a forked subagent context. |
| `agent` | Which subagent type to use when `context: fork` is set (e.g., `Explore`, `Plan`, `general-purpose`, or a custom agent). |
| `hooks` | Hooks scoped to this skill's lifecycle. |

String substitutions available in skill content: `$ARGUMENTS`, `$ARGUMENTS[N]` / `$N`, `${CLAUDE_SESSION_ID}`.

## Claude Code Plugin Documentation

- [Create plugins](https://docs.anthropic.com/en/docs/claude-code/plugins)
- [Plugins reference](https://docs.anthropic.com/en/docs/claude-code/plugins-reference)
- [Skills](https://docs.anthropic.com/en/docs/claude-code/skills)
- [Plugin marketplaces](https://docs.anthropic.com/en/docs/claude-code/plugin-marketplaces)
