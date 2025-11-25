# Husky Skill

## Purpose
Generate Husky configuration for Git hooks.

## Output
Create the file: `.husky/pre-commit`

## Example File
See: `examples.md` in this directory for complete examples and setup instructions.

## Template

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
npx stylelint src/**/*.vue src/**/*.scss
npm run test:unit
npx lint-staged
```

## Additional File: package.json addition

Add to package.json:

```json
{
  "lint-staged": {
    "*.{js,ts,vue}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,scss}": [
      "prettier --write"
    ],
    "*.{json,md}": [
      "prettier --write"
    ]
  }
}
```

## Notes
- Runs ESLint via `npm run lint` to check all code
- Runs Stylelint to check Vue and SCSS files for style issues
- Runs unit tests via `npm run test:unit` to ensure code changes don't break tests
- Runs lint-staged to auto-fix and format staged files
- Husky must be initialized with `npm run prepare` after install
- Pre-commit hook ensures code quality before commits are made
