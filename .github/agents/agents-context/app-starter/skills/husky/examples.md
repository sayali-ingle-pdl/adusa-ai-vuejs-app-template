# Husky Examples

## Purpose
Provides complete examples for Husky Git hooks configuration.

## Pre-Commit Hook

### File: `.husky/pre-commit`

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
npx stylelint src/**/*.vue src/**/*.scss
npm run test:unit
npx lint-staged
```

## Setup Instructions

### Step 1: Install Husky
Husky should be installed as a dev dependency in package.json:
```json
{
  "devDependencies": {
    "husky": "^9.1.7"
  }
}
```

### Step 2: Add Prepare Script
Add the prepare script to package.json:
```json
{
  "scripts": {
    "prepare": "husky"
  }
}
```

### Step 3: Initialize Husky
After npm install, run:
```bash
npm run prepare
```

### Step 4: Create Pre-Commit Hook
Create the file `.husky/pre-commit` with the content shown above.

### Step 5: Make Hook Executable
```bash
chmod +x .husky/pre-commit
```

## What the Pre-Commit Hook Does

1. **`npm run lint`** - Runs ESLint to check JavaScript/TypeScript/Vue files for code quality issues
2. **`npx stylelint src/**/*.vue src/**/*.scss`** - Checks Vue and SCSS files for style issues
3. **`npm run test:unit`** - Runs unit tests to ensure changes don't break existing functionality
4. **`npx lint-staged`** - Auto-formats and fixes staged files based on lint-staged configuration

## Lint-Staged Configuration

Add to `package.json`:
```json
{
  "lint-staged": {
    "*.{js,ts,vue}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,scss,vue}": [
      "stylelint --fix",
      "prettier --write"
    ],
    "*.{json,md}": [
      "prettier --write"
    ]
  }
}
```

## Hook Execution Flow

```
Developer runs: git commit -m "message"
    │
    ├─→ Husky triggers .husky/pre-commit
    │
    ├─→ Step 1: npm run lint (ESLint check)
    │   ├─ ✓ Pass → Continue
    │   └─ ✗ Fail → Abort commit
    │
    ├─→ Step 2: npx stylelint (Style check)
    │   ├─ ✓ Pass → Continue
    │   └─ ✗ Fail → Abort commit
    │
    ├─→ Step 3: npm run test:unit (Unit tests)
    │   ├─ ✓ Pass → Continue
    │   └─ ✗ Fail → Abort commit
    │
    ├─→ Step 4: npx lint-staged (Auto-fix & format)
    │   ├─ ✓ Pass → Continue
    │   └─ ✗ Fail → Abort commit
    │
    └─→ All checks passed → Commit succeeds
```

## Expected Output

### Successful Commit
```bash
$ git commit -m "feat: add new feature"

> npm run lint
✓ ESLint passed

> npx stylelint src/**/*.vue src/**/*.scss
✓ Stylelint passed

> npm run test:unit
✓ 42 tests passed

> npx lint-staged
✓ Preparing lint-staged...
✓ Running tasks for staged files...
✓ Applying modifications from tasks...
✓ Cleaning up temporary files...

[main abc1234] feat: add new feature
 2 files changed, 15 insertions(+), 3 deletions(-)
```

### Failed Commit (ESLint Error)
```bash
$ git commit -m "fix: update component"

> npm run lint
✗ ESLint failed
  /src/components/MyComponent.vue
    12:5  error  'unused' is assigned a value but never used  no-unused-vars

husky - pre-commit hook exited with code 1 (error)
```

## Troubleshooting

### Hook Not Running
If the pre-commit hook doesn't run:
1. Ensure Husky is initialized: `npm run prepare`
2. Check hook is executable: `chmod +x .husky/pre-commit`
3. Verify `.git/config` has core.hooksPath set

### Skipping Hooks (Emergency)
To bypass hooks in emergency situations:
```bash
git commit --no-verify -m "emergency fix"
```

**⚠️ Warning**: Only use `--no-verify` when absolutely necessary, as it bypasses all quality checks.

## Best Practices

1. **Keep hooks fast** - Long-running hooks slow down development
2. **Run tests incrementally** - Consider only running tests for changed files
3. **Auto-fix when possible** - Use lint-staged to automatically fix issues
4. **Fail fast** - Put fastest checks first (linting before tests)
5. **Provide clear error messages** - Help developers understand what failed

## Related Files

- `.husky/pre-commit` - The actual hook script
- `package.json` - Scripts and lint-staged configuration
- `.eslintrc.cjs` or `eslint.config.cjs` - ESLint configuration
- `.stylelintrc.json` - Stylelint configuration
- `.prettierrc` - Prettier configuration

## Testing the Hook

### Manual Test
```bash
# Make a change with an error
echo "const unused = 'test';" >> src/App.vue

# Stage the file
git add src/App.vue

# Try to commit
git commit -m "test commit"
# Should fail due to unused variable

# Fix the error
git restore src/App.vue
```

### Verify Hook Installation
```bash
# Check if hook exists
ls -la .husky/pre-commit

# View hook content
cat .husky/pre-commit

# Test hook directly
.husky/pre-commit
```
