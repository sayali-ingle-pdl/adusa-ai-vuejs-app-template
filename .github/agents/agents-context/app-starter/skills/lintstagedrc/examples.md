# Lint-Staged Configuration Examples

## Example: .lintstagedrc.json

```json
{
  "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
  "*.{json,md,yml,yaml}": ["prettier --write"]
}
```

## What Each Configuration Does

### 1. Code Files: `*.{js,ts,vue}`
```json
"*.{js,ts,vue}": ["eslint --fix", "prettier --write"]
```

**File Types**:
- `*.js` - JavaScript files
- `*.ts` - TypeScript files
- `*.vue` - Vue single-file components

**Commands**:
1. **`eslint --fix`**: 
   - Runs ESLint on staged files
   - Automatically fixes linting errors where possible
   - Catches code quality issues, bugs, and anti-patterns
   - Uses project's `.eslintrc` or `eslint.config.cjs` configuration

2. **`prettier --write`**:
   - Formats code according to Prettier rules
   - Ensures consistent code style
   - Runs after ESLint to apply final formatting
   - Uses project's `.prettierrc` configuration

### 2. Configuration & Documentation Files: `*.{json,md,yml,yaml}`
```json
"*.{json,md,yml,yaml}": ["prettier --write"]
```

**File Types**:
- `*.json` - JSON configuration files (package.json, tsconfig.json, etc.)
- `*.md` - Markdown documentation files
- `*.yml`, `*.yaml` - YAML configuration files

**Commands**:
1. **`prettier --write`**:
   - Formats configuration and documentation files
   - Ensures consistent formatting across project
   - No ESLint needed (these aren't code files)

## How Lint-Staged Works

### Workflow
1. Developer stages files: `git add file.ts`
2. Developer commits: `git commit -m "message"`
3. Husky pre-commit hook triggers
4. Lint-staged runs on staged files only
5. Commands execute for matching file patterns
6. If all pass, commit proceeds
7. If any fail, commit is blocked

### Example Execution
```bash
# Stage a file
git add src/components/MyComponent.vue

# Commit triggers lint-staged
git commit -m "Add new component"

# Lint-staged runs:
✔ eslint --fix src/components/MyComponent.vue
✔ prettier --write src/components/MyComponent.vue

# Commit succeeds
```

## Installation

### Install Dependencies
```bash
npm install --save-dev lint-staged
```

### Integration with Husky
In `.husky/pre-commit`:
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

## Advanced Configurations

### With CSS/SCSS Files
```json
{
  "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
  "*.{css,scss}": ["stylelint --fix", "prettier --write"],
  "*.{json,md,yml,yaml}": ["prettier --write"]
}
```

### With Testing
```json
{
  "*.{js,ts,vue}": [
    "eslint --fix",
    "prettier --write",
    "npm run test:unit -- --findRelatedTests"
  ],
  "*.{json,md,yml,yaml}": ["prettier --write"]
}
```

### With Type Checking
```json
{
  "*.{ts,vue}": [
    "eslint --fix",
    "prettier --write",
    "tsc --noEmit"
  ],
  "*.{js,json,md,yml,yaml}": ["prettier --write"]
}
```

### Separate Commands by File Type
```json
{
  "*.js": ["eslint --fix", "prettier --write"],
  "*.ts": ["eslint --fix", "prettier --write", "tsc --noEmit"],
  "*.vue": ["eslint --fix", "prettier --write"],
  "*.{css,scss}": ["stylelint --fix", "prettier --write"],
  "*.json": ["prettier --write"],
  "*.md": ["prettier --write", "markdownlint"],
  "*.{yml,yaml}": ["prettier --write"]
}
```

## Alternative Configuration Formats

### package.json
```json
{
  "lint-staged": {
    "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
    "*.{json,md,yml,yaml}": ["prettier --write"]
  }
}
```

### .lintstagedrc.js (JavaScript)
```javascript
module.exports = {
  '*.{js,ts,vue}': ['eslint --fix', 'prettier --write'],
  '*.{json,md,yml,yaml}': ['prettier --write'],
};
```

### .lintstagedrc.cjs (CommonJS)
```javascript
module.exports = {
  '*.{js,ts,vue}': ['eslint --fix', 'prettier --write'],
  '*.{json,md,yml,yaml}': ['prettier --write'],
};
```

### .lintstagedrc.mjs (ES Module)
```javascript
export default {
  '*.{js,ts,vue}': ['eslint --fix', 'prettier --write'],
  '*.{json,md,yml,yaml}': ['prettier --write'],
};
```

## Pattern Matching

### Glob Patterns
- `*.js` - All JavaScript files
- `**/*.js` - All JavaScript files in any directory
- `src/**/*.ts` - All TypeScript files in src directory
- `*.{js,ts}` - All JavaScript and TypeScript files

### Negation
```json
{
  "*.{js,ts}": ["eslint --fix"],
  "!dist/**": []
}
```

### Multiple Patterns
```json
{
  "src/**/*.{js,ts}": ["eslint --fix"],
  "tests/**/*.{js,ts}": ["eslint --fix", "jest --findRelatedTests"]
}
```

## Command Options

### ESLint Options
```json
{
  "*.{js,ts,vue}": [
    "eslint --fix --max-warnings=0",
    "prettier --write"
  ]
}
```
- `--fix`: Auto-fix problems
- `--max-warnings=0`: Fail if any warnings

### Prettier Options
```json
{
  "*.{js,ts,vue}": [
    "eslint --fix",
    "prettier --write --ignore-unknown"
  ]
}
```
- `--write`: Write changes to files
- `--ignore-unknown`: Ignore files without parser

## Running Lint-Staged Manually

### Run on All Staged Files
```bash
npx lint-staged
```

### Run on Specific Files
```bash
npx lint-staged --relative src/components/MyComponent.vue
```

### Dry Run (No Changes)
```bash
npx lint-staged --dry-run
```

### Debug Mode
```bash
npx lint-staged --debug
```

## Troubleshooting

### Lint-Staged Not Running
- Verify Husky is installed: `npm run prepare`
- Check `.husky/pre-commit` exists and is executable
- Ensure `lint-staged` is in `devDependencies`

### Commands Failing
- Run commands manually to verify they work
- Check ESLint and Prettier configurations
- Ensure all dependencies are installed

### Files Not Being Linted
- Verify glob patterns match your files
- Check that files are actually staged: `git status`
- Use `--verbose` flag to see what's being processed

### Performance Issues
- Limit patterns to necessary directories
- Avoid running expensive operations (like full test suite)
- Use `--concurrent` for parallel execution

### Commits Still Succeed Despite Errors
- Check exit codes of commands
- Ensure Husky pre-commit hook has correct content
- Verify hook is executable: `chmod +x .husky/pre-commit`

## Best Practices

### 1. Fast Commands Only
- Only run quick checks in pre-commit
- Avoid full test suites (use `--findRelatedTests` instead)
- Skip slow type checking in pre-commit

### 2. Auto-Fix When Possible
- Use `--fix` flags to auto-correct issues
- Let tools fix formatting automatically
- Reduces developer friction

### 3. Keep It Simple
- Start with basic ESLint and Prettier
- Add more checks gradually
- Don't overwhelm developers

### 4. Document Requirements
- Explain what lint-staged does in README
- Provide instructions for bypassing (emergencies only)
- Document how to fix common errors

### 5. Consistent Configuration
- Use same ESLint/Prettier config for all developers
- Commit configuration files to repository
- Use exact versions in package.json

## Integration with CI/CD

### GitHub Actions
```yaml
name: Lint
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm run format:check
```

### Bypass in Emergencies
```bash
# Use with caution
git commit --no-verify -m "Emergency fix"
```

## Performance Optimization

### Concurrent Execution
```json
{
  "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
  "concurrent": true
}
```

### Limited Concurrency
```javascript
module.exports = {
  '*.{js,ts,vue}': ['eslint --fix', 'prettier --write'],
  concurrent: 2, // Run 2 tasks at a time
};
```

### Skip Ignored Files
Lint-staged automatically respects `.eslintignore` and `.prettierignore`.

## Related Tools

### Husky
Git hooks management - triggers lint-staged

### ESLint
JavaScript/TypeScript linting

### Prettier
Code formatting

### Stylelint
CSS/SCSS linting

## Complete Setup Example

### 1. Install Dependencies
```bash
npm install --save-dev husky lint-staged eslint prettier
```

### 2. Initialize Husky
```bash
npx husky install
npm pkg set scripts.prepare="husky install"
```

### 3. Create Pre-Commit Hook
```bash
npx husky add .husky/pre-commit "npx lint-staged"
```

### 4. Create .lintstagedrc.json
```json
{
  "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
  "*.{json,md,yml,yaml}": ["prettier --write"]
}
```

### 5. Test
```bash
git add .
git commit -m "Test lint-staged"
```
