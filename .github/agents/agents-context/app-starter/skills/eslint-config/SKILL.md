# ESLint Config Skill

## Purpose
Generate the `eslint.config.cjs` file for code linting with Vue and TypeScript support.

## Output
Create the file: `eslint.config.cjs`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

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

## Notes
- Uses ESLint 9 flat config format
- Integrates Vue, TypeScript, and Prettier
- Console and debugger allowed in development
- Special rules for test and declaration files
