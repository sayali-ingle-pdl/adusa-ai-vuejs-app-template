# Husky Skill

## Purpose
Generate Husky configuration for Git hooks.

## Output
Create the file: `.husky/pre-commit`

## Template
See: `examples.md` in this directory for complete templates and setup instructions.

## Notes
- Runs ESLint via `npm run lint` to check all code
- Runs Stylelint to check Vue and SCSS files for style issues
- Runs unit tests via `npm run test:unit` to ensure code changes don't break tests
- Runs lint-staged to auto-fix and format staged files
- Husky must be initialized with `npm run prepare` after install
- Pre-commit hook ensures code quality before commits are made
