# Husky Pre-Commit Hook Examples

## Example: .husky/pre-commit

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
npx stylelint src/**/*.vue src/**/*.scss
npm run test:unit
npx lint-staged
```

## What Each Command Does

### 1. `npm run lint`
- Runs ESLint to check JavaScript, TypeScript, and Vue files
- Ensures code follows linting rules and best practices
- Catches potential errors before commit

### 2. `npx stylelint src/**/*.vue src/**/*.scss`
- Checks Vue component styles and SCSS files
- Enforces CSS/SCSS style guidelines
- Ensures consistent styling patterns

### 3. `npm run test:unit`
- Runs Jest unit tests
- Ensures code changes don't break existing tests
- Validates that all tests pass before commit

### 4. `npx lint-staged`
- Automatically fixes and formats staged files
- Runs ESLint --fix on JavaScript/TypeScript/Vue files
- Runs Prettier on all staged files
- Only processes files that are staged for commit

## Setup Instructions

1. Install husky and lint-staged:
```bash
npm install --save-dev husky lint-staged
```

2. Initialize husky:
```bash
npx husky install
```

3. Create the pre-commit hook:
```bash
npx husky add .husky/pre-commit "npm run lint"
npx husky add .husky/pre-commit "npx stylelint src/**/*.vue src/**/*.scss"
npx husky add .husky/pre-commit "npm run test:unit"
npx husky add .husky/pre-commit "npx lint-staged"
```

Or manually create `.husky/pre-commit` with the content shown above.

4. Make the hook executable:
```bash
chmod +x .husky/pre-commit
```

5. Add prepare script to package.json:
```json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```

## Expected Behavior

When you run `git commit`:
1. ESLint runs on all files
2. Stylelint checks Vue and SCSS files
3. Unit tests execute
4. Lint-staged auto-fixes and formats staged files
5. If all checks pass, commit proceeds
6. If any check fails, commit is blocked

## Troubleshooting

### Hook Not Running
- Ensure husky is installed: `npm run prepare`
- Check file permissions: `chmod +x .husky/pre-commit`

### Tests Taking Too Long
- Consider running only affected tests
- Use `--bail` flag to stop on first failure

### Stylelint Errors
- Ensure stylelint is configured in project
- Check `.stylelintrc` or stylelint config in package.json
