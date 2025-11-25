# Prettier Configuration Examples

## Example: .prettierrc

```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

## What Each Option Does

### 1. `"singleQuote": true`
- **Purpose**: Use single quotes instead of double quotes for strings
- **Default**: `false` (double quotes)
- **Impact**: All string literals use single quotes

**Examples:**
```javascript
// With singleQuote: true
const message = 'Hello, world!';
const name = 'John';

// With singleQuote: false (default)
const message = "Hello, world!";
const name = "John";
```

**Why use it:**
- Less visual noise (no shift key needed)
- Common in JavaScript community
- Consistent with many popular style guides (Airbnb, Standard)

### 2. `"semi": true`
- **Purpose**: Add semicolons at the end of statements
- **Default**: `true`
- **Impact**: All statements end with semicolons

**Examples:**
```javascript
// With semi: true
const name = 'John';
const age = 30;
console.log(name);

// With semi: false
const name = 'John'
const age = 30
console.log(name)
```

**Why use it:**
- Prevents ASI (Automatic Semicolon Insertion) bugs
- More explicit and clear
- Traditional JavaScript style
- Safer for code minification

### 3. `"trailingComma": "es5"`
- **Purpose**: Add trailing commas where valid in ES5
- **Default**: `"all"`
- **Options**: `"none"`, `"es5"`, `"all"`
- **Impact**: Adds commas after last item in objects, arrays, function parameters (ES5 compatible)

**Examples:**
```javascript
// With trailingComma: "es5"
const obj = {
  name: 'John',
  age: 30,  // ✅ Trailing comma
};

const arr = [
  'apple',
  'banana',
  'orange',  // ✅ Trailing comma
];

// Function parameters - no trailing comma (not ES5)
function greet(
  name,
  age  // ❌ No trailing comma
) {}

// With trailingComma: "all"
function greet(
  name,
  age,  // ✅ Trailing comma even in function params
) {}

// With trailingComma: "none"
const obj = {
  name: 'John',
  age: 30  // ❌ No trailing comma
};
```

**Why use "es5":**
- Better git diffs (adding items doesn't modify previous line)
- Easier to reorder items
- ES5 compatible (works in all modern environments)
- Safe for older browsers if transpiling

## Prettier's Other Default Settings

These are used automatically (no need to specify):

```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "bracketSpacing": true,
  "bracketSameLine": false,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

### Common Defaults Explained

- **`printWidth: 80`**: Wrap lines longer than 80 characters
- **`tabWidth: 2`**: Use 2 spaces for indentation
- **`useTabs: false`**: Use spaces, not tabs
- **`bracketSpacing: true`**: Add spaces in object literals `{ foo: bar }`
- **`arrowParens: "always"`**: Always use parentheses in arrow functions `(x) => x`
- **`endOfLine: "lf"`**: Use Unix line endings

## Alternative Configuration Formats

### .prettierrc (JSON without extension)
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

### .prettierrc.json (with extension)
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

### .prettierrc.js (JavaScript)
```javascript
module.exports = {
  singleQuote: true,
  semi: true,
  trailingComma: 'es5',
};
```

### .prettierrc.cjs (CommonJS)
```javascript
module.exports = {
  singleQuote: true,
  semi: true,
  trailingComma: 'es5',
};
```

### .prettierrc.mjs (ES Module)
```javascript
export default {
  singleQuote: true,
  semi: true,
  trailingComma: 'es5',
};
```

### .prettierrc.yaml
```yaml
singleQuote: true
semi: true
trailingComma: es5
```

### package.json
```json
{
  "prettier": {
    "singleQuote": true,
    "semi": true,
    "trailingComma": "es5"
  }
}
```

## Extended Configuration Examples

### Minimal (Using Defaults)
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

### Common Additional Options
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5",
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false
}
```

### With Vue-Specific Settings
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5",
  "vueIndentScriptAndStyle": false
}
```

### With Override for Specific Files
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5",
  "overrides": [
    {
      "files": "*.json",
      "options": {
        "trailingComma": "none"
      }
    },
    {
      "files": "*.md",
      "options": {
        "printWidth": 100
      }
    }
  ]
}
```

## Integration with ESLint

### eslint-config-prettier
Install to disable conflicting ESLint rules:
```bash
npm install --save-dev eslint-config-prettier
```

**eslint.config.js:**
```javascript
import prettier from 'eslint-config-prettier';

export default [
  // ... other configs
  prettier, // Must be last to override other configs
];
```

### eslint-plugin-prettier
Run Prettier as an ESLint rule:
```bash
npm install --save-dev eslint-plugin-prettier
```

**eslint.config.js:**
```javascript
import prettier from 'eslint-plugin-prettier/recommended';

export default [
  prettier,
];
```

## Usage

### Format All Files
```bash
npx prettier --write .
```

### Check Formatting
```bash
npx prettier --check .
```

### Format Specific Files
```bash
npx prettier --write "src/**/*.{js,ts,vue,json}"
```

### Format Staged Files (with lint-staged)
```json
{
  "lint-staged": {
    "*.{js,ts,vue,json}": ["prettier --write"]
  }
}
```

## Editor Integration

### VS Code
Install "Prettier - Code formatter" extension

**.vscode/settings.json:**
```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[vue]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

### WebStorm/IntelliJ IDEA
1. Settings → Languages & Frameworks → JavaScript → Prettier
2. Check "On 'Reformat Code' action"
3. Check "On save"

### Vim/Neovim
Use plugins like:
- `prettier/vim-prettier`
- `neovim/nvim-lspconfig` with Prettier LSP

## package.json Scripts

```json
{
  "scripts": {
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "format:fix": "prettier --write .",
    "lint": "eslint . --fix",
    "lint:format": "npm run lint && npm run format"
  }
}
```

## CI/CD Integration

### GitHub Actions
```yaml
name: Format Check
on: [push, pull_request]
jobs:
  prettier:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run format:check
```

### Pre-commit Hook (Husky)
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

**.lintstagedrc.json:**
```json
{
  "*.{js,ts,vue,json,md}": ["prettier --write"]
}
```

## Troubleshooting

### Conflicting with ESLint
Install `eslint-config-prettier` to disable conflicting rules.

### Not Formatting on Save
- Check editor extension is installed
- Verify `.prettierrc` is in project root
- Check editor settings for "format on save"

### Different Formatting in CI vs Local
- Ensure same Prettier version in package.json
- Lock dependencies with `npm ci` instead of `npm install`
- Commit `.prettierrc` to repository

### Trailing Commas in JSON
JSON doesn't support trailing commas. Use overrides:
```json
{
  "trailingComma": "es5",
  "overrides": [
    {
      "files": "*.json",
      "options": {
        "trailingComma": "none"
      }
    }
  ]
}
```

## Best Practices

### 1. Commit Configuration
Always commit `.prettierrc` to ensure team consistency.

### 2. Use Exact Versions
```json
{
  "devDependencies": {
    "prettier": "3.1.0"
  }
}
```

### 3. Combine with .prettierignore
Create `.prettierignore` to exclude generated files:
```
dist/
node_modules/
coverage/
*.min.js
```

### 4. Run Before Commit
Use Husky + lint-staged for automatic formatting.

### 5. Document in README
```markdown
## Code Formatting

This project uses Prettier with the following configuration:
- Single quotes
- Semicolons required
- ES5 trailing commas

Format code: `npm run format`
```

### 6. Keep It Simple
Start with minimal configuration and add options only when needed.

## Comparison with Other Configs

### Standard Style (No Semicolons)
```json
{
  "singleQuote": true,
  "semi": false,
  "trailingComma": "es5"
}
```

### Airbnb Style
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "all",
  "printWidth": 100
}
```

### Google Style
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5",
  "printWidth": 80
}
```

## Migration from Other Formatters

### From Beautify
Prettier is opinionated and has fewer options. Accept defaults.

### From StandardJS
Change `"semi": false` to `"semi": true` if adopting semicolons.

### From ESLint --fix
Prettier formats, ESLint finds bugs. Use both together.

## Common Questions

### Why single quotes?
- Less visual clutter
- Common in JavaScript community
- No functional difference

### Why semicolons?
- Prevents ASI bugs
- More explicit
- Safer for minification

### Why ES5 trailing commas?
- Better git diffs
- Easier to add/remove items
- Compatible with all modern browsers

### Can I disable Prettier for specific code?
```javascript
// prettier-ignore
const ugly = { a:1,b:2,c:3 };

/* prettier-ignore */
function messy() {
  return { x:1,y:2 };
}
```

## Documentation Template

Add to README.md:

```markdown
## Code Formatting

This project uses Prettier for consistent code formatting.

### Configuration
- Single quotes for strings
- Semicolons required
- ES5 trailing commas

### Format Code
```bash
npm run format
```

### Check Formatting
```bash
npm run format:check
```

### Auto-format on Save
Install Prettier extension in your editor and enable "format on save".
```

## Useful Resources

- [Prettier Documentation](https://prettier.io/docs/en/)
- [Prettier Playground](https://prettier.io/playground/)
- [Prettier Options](https://prettier.io/docs/en/options.html)
- [Why Prettier?](https://prettier.io/docs/en/why-prettier.html)
