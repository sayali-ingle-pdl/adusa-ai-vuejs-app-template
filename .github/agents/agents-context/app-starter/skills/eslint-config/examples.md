# ESLint Configuration Examples

## Example: eslint.config.cjs

```javascript
const globals = require('globals');
const vuePlugin = require('eslint-plugin-vue');
const vueParser = require('vue-eslint-parser');
const tsPlugin = require('@typescript-eslint/eslint-plugin');
const prettierPlugin = require('eslint-plugin-prettier');
const tsParser = require('@typescript-eslint/parser');

const browserNode = { ...globals.browser, ...globals.node };

module.exports = [
  {
    files: ['**/*.{js,ts,vue}'],
    ignores: ['node_modules', 'dist'],
    languageOptions: {
      ecmaVersion: 'latest',
      sourceType: 'module',
      parser: vueParser,
      parserOptions: {
        parser: tsParser,
        ecmaVersion: 'latest',
        sourceType: 'module',
      },
      globals: browserNode,
    },
    plugins: {
      vue: vuePlugin,
      '@typescript-eslint': tsPlugin,
      prettier: prettierPlugin,
    },
    rules: {
      'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
      'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
      '@typescript-eslint/no-unused-vars': [
        'warn',
        { argsIgnorePattern: '^_', caughtErrors: 'none' },
      ],
      '@typescript-eslint/ban-types': 'off',
      '@typescript-eslint/no-explicit-any': 'warn',
      'prettier/prettier': ['error', { endOfLine: 'auto' }],
    },
  },
  {
    files: ['**/__tests__/*.{j,t}s?(x)', '**/*.spec.{j,t}s?(x)'],
    languageOptions: { globals: { ...globals.jest, ...globals.node } },
  },
  {
    files: ['**/*.d.ts'],
    rules: {
      '@typescript-eslint/no-explicit-any': 'off',
      '@typescript-eslint/no-empty-interface': 'off',
      '@typescript-eslint/no-empty-object-type': 'off',
    },
  },
];
```

## What Each Section Does

### Required Imports
```javascript
const globals = require('globals');
const vuePlugin = require('eslint-plugin-vue');
const vueParser = require('vue-eslint-parser');
const tsPlugin = require('@typescript-eslint/eslint-plugin');
const prettierPlugin = require('eslint-plugin-prettier');
const tsParser = require('@typescript-eslint/parser');
```

**Dependencies:**
- **globals**: Predefined global variables for different environments
- **eslint-plugin-vue**: Vue.js specific linting rules
- **vue-eslint-parser**: Parser for `.vue` files
- **@typescript-eslint/eslint-plugin**: TypeScript linting rules
- **eslint-plugin-prettier**: Runs Prettier as an ESLint rule
- **@typescript-eslint/parser**: Parser for TypeScript

### Global Variables Setup
```javascript
const browserNode = { ...globals.browser, ...globals.node };
```
- **Purpose**: Combines browser and Node.js global variables
- **Why**: Vue apps run in browser but build/test in Node
- **Examples**: `window`, `document`, `process`, `__dirname`

## Main Configuration Object

### File Targeting and Ignores
```javascript
files: ['**/*.{js,ts,vue}'],
ignores: ['node_modules', 'dist'],
```

**files:**
- Applies rules to JavaScript, TypeScript, and Vue files
- `**/*` matches files in all directories

**ignores:**
- Skips linting in `node_modules` (dependencies)
- Skips linting in `dist` (build output)

### Language Options
```javascript
languageOptions: {
  ecmaVersion: 'latest',
  sourceType: 'module',
  parser: vueParser,
  parserOptions: {
    parser: tsParser,
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  globals: browserNode,
},
```

**ecmaVersion: 'latest':**
- Supports newest JavaScript features
- Auto-updates with ESLint

**sourceType: 'module':**
- Uses ES modules (import/export)
- Not CommonJS (require/module.exports)

**parser: vueParser:**
- Primary parser for `.vue` files
- Handles `<template>`, `<script>`, `<style>` blocks

**parserOptions.parser: tsParser:**
- TypeScript parser for `<script>` blocks
- Handles TypeScript syntax

**globals: browserNode:**
- Defines available global variables
- Prevents "undefined variable" errors

### Plugins
```javascript
plugins: {
  vue: vuePlugin,
  '@typescript-eslint': tsPlugin,
  prettier: prettierPlugin,
},
```

**vue:**
- Vue-specific linting rules
- Component naming, template syntax, etc.

**@typescript-eslint:**
- TypeScript-specific rules
- Type checking, unused variables, etc.

**prettier:**
- Formatting rules
- Integrates code formatting with linting

## Rules Explained

### Console and Debugger
```javascript
'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
```

**Development:**
- `console.log()` allowed ✅
- `debugger` statements allowed ✅
- Helps with debugging

**Production:**
- `console.log()` shows warning ⚠️
- `debugger` shows warning ⚠️
- Reminds to remove before deployment

### Unused Variables
```javascript
'@typescript-eslint/no-unused-vars': [
  'warn',
  { argsIgnorePattern: '^_', caughtErrors: 'none' },
],
```

**What it does:**
- Warns about unused variables
- **argsIgnorePattern: '^_'**: Ignores variables starting with `_`
- **caughtErrors: 'none'**: Doesn't warn about unused catch parameters

**Examples:**
```typescript
// ⚠️ Warning
const unused = 'value';

// ✅ OK - starts with underscore
const _unused = 'value';

// ✅ OK - catch parameter ignored
try {
  doSomething();
} catch (error) {
  console.log('Error occurred');
}
```

### TypeScript Rules

#### Ban Types
```javascript
'@typescript-eslint/ban-types': 'off',
```
- Allows using `Function`, `Object`, etc.
- More flexible for certain patterns

#### Explicit Any
```javascript
'@typescript-eslint/no-explicit-any': 'warn',
```
- Warns when using `any` type
- Encourages proper typing
- Still allows `any` when necessary

**Examples:**
```typescript
// ⚠️ Warning - should specify type
function process(data: any) { }

// ✅ Better
function process(data: unknown) { }
function process<T>(data: T) { }
```

### Prettier Integration
```javascript
'prettier/prettier': ['error', { endOfLine: 'auto' }],
```
- Runs Prettier formatting as ESLint rule
- **endOfLine: 'auto'**: Handles different OS line endings
- Errors on formatting violations

## Test Files Configuration
```javascript
{
  files: ['**/__tests__/*.{j,t}s?(x)', '**/*.spec.{j,t}s?(x)'],
  languageOptions: { globals: { ...globals.jest, ...globals.node } },
}
```

**Applies to:**
- Files in `__tests__/` directories
- Files ending with `.spec.js`, `.spec.ts`, etc.

**Adds globals:**
- Jest functions: `describe`, `it`, `expect`, `beforeEach`, etc.
- Node.js globals: `process`, `__dirname`, etc.

**Example:**
```typescript
// test.spec.ts - no errors for Jest globals
describe('Component', () => {
  it('should work', () => {
    expect(true).toBe(true);
  });
});
```

## TypeScript Declaration Files
```javascript
{
  files: ['**/*.d.ts'],
  rules: {
    '@typescript-eslint/no-explicit-any': 'off',
    '@typescript-eslint/no-empty-interface': 'off',
    '@typescript-eslint/no-empty-object-type': 'off',
  },
}
```

**Relaxed rules for `.d.ts` files:**
- `any` type allowed (often needed for declarations)
- Empty interfaces allowed (augmentation patterns)
- Empty object types allowed (type utilities)

**Example:**
```typescript
// vite-env.d.ts - no warnings
declare module '*.vue' {
  import type { DefineComponent } from 'vue';
  const component: DefineComponent<{}, {}, any>;
  export default component;
}
```

## Installation

### Required Dependencies
```bash
npm install --save-dev \
  eslint \
  globals \
  eslint-plugin-vue \
  vue-eslint-parser \
  @typescript-eslint/eslint-plugin \
  @typescript-eslint/parser \
  eslint-plugin-prettier \
  eslint-config-prettier
```

### package.json
```json
{
  "devDependencies": {
    "eslint": "^9.0.0",
    "globals": "^15.0.0",
    "eslint-plugin-vue": "^9.0.0",
    "vue-eslint-parser": "^9.0.0",
    "@typescript-eslint/eslint-plugin": "^8.0.0",
    "@typescript-eslint/parser": "^8.0.0",
    "eslint-plugin-prettier": "^5.0.0",
    "eslint-config-prettier": "^9.0.0"
  }
}
```

## Usage

### Lint All Files
```bash
npx eslint .
```

### Lint and Auto-Fix
```bash
npx eslint . --fix
```

### Lint Specific Files
```bash
npx eslint src/**/*.vue
npx eslint src/**/*.ts
```

## package.json Scripts

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "lint:check": "eslint . --max-warnings 0"
  }
}
```

## Editor Integration

### VS Code
Install "ESLint" extension

**.vscode/settings.json:**
```json
{
  "eslint.enable": true,
  "eslint.validate": [
    "javascript",
    "typescript",
    "vue"
  ],
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### WebStorm/IntelliJ IDEA
1. Settings → Languages & Frameworks → JavaScript → Code Quality Tools → ESLint
2. Check "Automatic ESLint configuration"
3. Enable "Run eslint --fix on save"

## Common Patterns

### Ignoring Specific Lines
```typescript
// eslint-disable-next-line no-console
console.log('Debug info');

/* eslint-disable @typescript-eslint/no-explicit-any */
function legacyFunction(data: any) { }
/* eslint-enable @typescript-eslint/no-explicit-any */
```

### Ignoring Entire Files
```typescript
/* eslint-disable */
// Entire file ignored
/* eslint-enable */
```

### Using Underscore Pattern
```typescript
// Function parameter ignored
function onClick(_event: Event, data: string) {
  console.log(data);
}

// Destructuring with ignored values
const { name, _id, email } = user;
```

## Integration with Prettier

### Why Both?
- **ESLint**: Code quality (bugs, best practices)
- **Prettier**: Code formatting (style, consistency)

### Configuration
Prettier config in `.prettierrc`:
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

ESLint uses these settings via `eslint-plugin-prettier`.

## Troubleshooting

### Issue: Parsing error
**Error:**
```
Parsing error: '>' expected
```

**Solution:**
Ensure correct parser is specified:
- Vue files: `vue-eslint-parser`
- TypeScript in Vue: `parserOptions.parser: tsParser`

### Issue: Unknown global variables
**Error:**
```
'describe' is not defined
```

**Solution:**
Add appropriate config block (test files, etc.)

### Issue: Prettier conflicts
**Error:**
```
Delete `␍` prettier/prettier
```

**Solution:**
Set `endOfLine: 'auto'` in Prettier rule:
```javascript
'prettier/prettier': ['error', { endOfLine: 'auto' }]
```

### Issue: Too many warnings
**Solution:**
1. Fix incrementally
2. Use `.eslintignore` for legacy code
3. Adjust rule severity (error → warn → off)

## Best Practices

### 1. Fix Issues Incrementally
Don't disable all rules; fix issues over time.

### 2. Use Meaningful Underscore Pattern
```typescript
// Good - indicates intentionally unused
function handler(_event: Event) { }

// Bad - might be a typo
function process(_data: Data) {
  // Should data be used?
}
```

### 3. Add Comments for Disabled Rules
```typescript
// eslint-disable-next-line @typescript-eslint/no-explicit-any
// TODO: Add proper types after API is stable
const response: any = await fetchData();
```

### 4. Review Warnings Regularly
Don't let warnings accumulate.

### 5. Use Pre-commit Hooks
With Husky and lint-staged:
```json
{
  "lint-staged": {
    "*.{js,ts,vue}": ["eslint --fix", "prettier --write"]
  }
}
```

## ESLint 9 Flat Config vs Legacy

### Flat Config (eslint.config.cjs)
```javascript
module.exports = [
  {
    files: ['**/*.js'],
    rules: { }
  }
];
```

### Legacy (.eslintrc.js)
```javascript
module.exports = {
  extends: ['plugin:vue/vue3-recommended'],
  rules: { }
};
```

**Benefits of Flat Config:**
- More explicit configuration
- Better TypeScript support
- Easier to understand and debug
- Official ESLint 9 format

## Custom Rules Examples

### Stricter Configuration
```javascript
rules: {
  'no-console': 'error',  // Always error
  '@typescript-eslint/no-explicit-any': 'error',  // Prevent any
  '@typescript-eslint/no-unused-vars': 'error',  // Error on unused
}
```

### More Lenient
```javascript
rules: {
  'no-console': 'off',  // Allow everywhere
  '@typescript-eslint/no-explicit-any': 'off',  // Allow any
}
```

## Resources

- [ESLint Documentation](https://eslint.org/docs/latest/)
- [ESLint Flat Config](https://eslint.org/docs/latest/use/configure/configuration-files)
- [eslint-plugin-vue](https://eslint.vuejs.org/)
- [@typescript-eslint](https://typescript-eslint.io/)
- [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)
