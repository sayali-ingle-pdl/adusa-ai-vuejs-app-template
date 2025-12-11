# Lint-Staged Configuration Skill

## Purpose
Generate lint-staged configuration to automatically fix and format staged files before commit.

## Output
Create the file: `.lintstagedrc.json`

## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- Runs ESLint with auto-fix on JavaScript, TypeScript, and Vue files
- Runs Prettier formatting on code and configuration files
- Only processes files that are staged for commit (fast and efficient)
- Works with Husky pre-commit hook for automated quality checks
- Prevents committing code that doesn't meet linting and formatting standards
- Multiple commands run sequentially for each file pattern
