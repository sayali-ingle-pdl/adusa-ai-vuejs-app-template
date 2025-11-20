# Husky Skill

## Purpose
Generate Husky configuration for Git hooks.

## Output
Create the file: `.husky/pre-commit`

## Template

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

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
- Runs lint-staged on pre-commit
- Automatically fixes linting issues
- Formats code with Prettier
- Only processes staged files for performance
- Husky must be initialized with `npm run prepare` after install
