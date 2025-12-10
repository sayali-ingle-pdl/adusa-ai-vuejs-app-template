# Lint-Staged Configuration Skill

## Purpose
Generate lint-staged configuration to automatically fix and format staged files before commit.

## Output
Create the file: `.lintstagedrc.json`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Notes
- Runs ESLint with auto-fix on JavaScript, TypeScript, and Vue files
- Runs Prettier formatting on code and configuration files
- Only processes files that are staged for commit (fast and efficient)
- Works with Husky pre-commit hook for automated quality checks
- Prevents committing code that doesn't meet linting and formatting standards
- Multiple commands run sequentially for each file pattern
