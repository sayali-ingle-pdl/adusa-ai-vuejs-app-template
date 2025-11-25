# Stylelint Configuration Examples

## Example: .stylelintrc

```json
{
  "extends": [
    "stylelint-config-standard-scss",
    "stylelint-config-recommended-vue"
  ],
  "rules": {
    "import-notation": "string",
    "color-function-notation": "legacy",
    "property-no-vendor-prefix": null,
    "alpha-value-notation": "number",
    "media-feature-range-notation": "prefix",
    "unit-allowed-list": [
      "em",
      "rem",
      "%",
      "px",
      "s",
      "in",
      "deg",
      "fr",
      "vh",
      "vw"
    ],
    "scss/at-extend-no-missing-placeholder": null,
    "no-invalid-position-at-import-rule": null,
    "no-descending-specificity": null,
    "selector-pseudo-element-colon-notation": null,
    "declaration-empty-line-before": null,
    "at-rule-empty-line-before": null,
    "value-keyword-case": null,
    "at-rule-no-unknown": null,
    "selector-class-pattern": null,
    "scss/dollar-variable-colon-space-after": "always",
    "declaration-property-value-no-unknown": null
  }
}
```

## What Each Section Does

### Extends

#### `"stylelint-config-standard-scss"`
- **Purpose**: Base configuration for SCSS/Sass linting
- **Provides**: Rules for SCSS syntax, variables, mixins, functions
- **Installation**: `npm install --save-dev stylelint-config-standard-scss`
- **Features**:
  - SCSS-specific syntax validation
  - Best practices for SCSS code
  - Standard formatting rules

#### `"stylelint-config-recommended-vue"`
- **Purpose**: Vue-specific style linting rules
- **Provides**: Rules for Vue single-file component `<style>` blocks
- **Installation**: `npm install --save-dev stylelint-config-recommended-vue`
- **Features**:
  - Parses `<style>` tags in `.vue` files
  - Supports scoped styles
  - Vue-specific style patterns

### Rules Explained

#### Import and Notation Rules

##### `"import-notation": "string"`
```scss
// Enforced
@import "variables";
@import "mixins";

// Not allowed
@import url("variables");
```
- Uses string notation for imports
- Cleaner, more readable imports

##### `"color-function-notation": "legacy"`
```scss
// Allowed (legacy)
color: rgba(0, 0, 0, 0.5);
background: hsla(120, 100%, 50%, 0.3);

// Not required (modern)
color: rgb(0 0 0 / 0.5);
background: hsl(120 100% 50% / 0.3);
```
- Uses traditional color function syntax
- Better browser compatibility
- More familiar to developers

##### `"alpha-value-notation": "number"`
```scss
// Enforced
opacity: 0.5;
color: rgba(0, 0, 0, 0.8);

// Not allowed
opacity: 50%;
color: rgba(0, 0, 0, 80%);
```
- Alpha values as decimals (0-1)
- Standard JavaScript/CSS convention

##### `"media-feature-range-notation": "prefix"`
```scss
// Enforced
@media (min-width: 768px) { }
@media (max-width: 1024px) { }

// Not required
@media (width >= 768px) { }
@media (width <= 1024px) { }
```
- Uses traditional prefix notation
- Better browser support
- More widely understood

#### Allowed Units

##### `"unit-allowed-list"`
```scss
// Allowed units
width: 100px;        // Pixels
font-size: 1.5rem;   // Root em
padding: 2em;        // Em
margin: 10%;         // Percentage
transition: 0.3s;    // Seconds
rotate: 45deg;       // Degrees
grid-template-columns: 1fr 2fr; // Fractional units
height: 100vh;       // Viewport height
width: 50vw;         // Viewport width
border-width: 1in;   // Inches (print)

// Not allowed
width: 100pt;        // Points
font-size: 12pc;     // Picas
```

**Why these units:**
- **px**: Precise pixel control
- **rem**: Scalable, root-based sizing
- **em**: Relative to parent font size
- **%**: Percentage-based layouts
- **s**: Animation/transition timing
- **deg**: Transformations and rotations
- **fr**: CSS Grid fractional units
- **vh/vw**: Viewport-based responsive design
- **in**: Print media support

#### SCSS-Specific Rules

##### `"scss/at-extend-no-missing-placeholder": null`
```scss
// Allowed - extending without placeholder
.button {
  @extend .btn-base;
}

// Also allowed - extending with placeholder
.button {
  @extend %btn-base;
}
```
- Allows extending classes directly
- More flexible SCSS usage

##### `"scss/dollar-variable-colon-space-after": "always"`
```scss
// Enforced
$primary-color: #007bff;
$font-size: 16px;

// Not allowed
$primary-color:#007bff;
$font-size:16px;
```
- Requires space after colon in SCSS variables
- Improves readability

#### Disabled Strict Rules

##### `"property-no-vendor-prefix": null`
```scss
// Allowed - vendor prefixes when needed
.element {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}
```
- Allows vendor prefixes for compatibility
- Useful for older browser support

##### `"no-invalid-position-at-import-rule": null`
```scss
// Allowed - imports anywhere
.component {
  color: red;
}

@import "mixins";
```
- Flexible import positioning
- Useful in complex SCSS structures

##### `"no-descending-specificity": null`
```scss
// Allowed - descending specificity
.parent .child { }
.child { }
```
- Prevents specificity warnings
- More practical for real-world CSS

##### `"selector-pseudo-element-colon-notation": null`
```scss
// Both allowed
.element::before { }
.element:before { }
```
- Allows both single and double colon notation
- Flexible for legacy code

##### `"declaration-empty-line-before": null`
```scss
// Both allowed
.element {
  color: red;
  background: blue;
}

.element {
  color: red;

  background: blue;
}
```
- No requirement for empty lines
- Developer preference

##### `"at-rule-empty-line-before": null`
```scss
// Both allowed
.element {
  color: red;
}
@media (min-width: 768px) { }

.element {
  color: red;
}

@media (min-width: 768px) { }
```
- Flexible spacing around at-rules

##### `"value-keyword-case": null`
```scss
// Both allowed
color: Red;
color: red;
display: Block;
display: block;
```
- Allows any case for keywords
- More lenient

##### `"at-rule-no-unknown": null`
```scss
// Allowed - custom at-rules
@tailwind base;
@apply flex items-center;
@layer components { }
```
- Allows custom/framework at-rules
- Useful for Tailwind, custom preprocessors

##### `"selector-class-pattern": null`
```scss
// Any class naming pattern allowed
.myClass { }
.my-class { }
.my_class { }
.MyClass { }
```
- No enforced naming convention
- Flexible for different methodologies (BEM, camelCase, etc.)

##### `"declaration-property-value-no-unknown": null`
```scss
// Allows experimental or custom property values
display: -webkit-box;
background: -webkit-linear-gradient(red, blue);
```
- Permits vendor-specific values
- Useful for cutting-edge CSS features

## Installation

### Required Dependencies
```bash
npm install --save-dev \
  stylelint \
  stylelint-config-standard-scss \
  stylelint-config-recommended-vue \
  postcss-html
```

### package.json
```json
{
  "devDependencies": {
    "stylelint": "^16.0.0",
    "stylelint-config-standard-scss": "^13.0.0",
    "stylelint-config-recommended-vue": "^1.5.0",
    "postcss-html": "^1.6.0"
  }
}
```

## Usage

### Lint All Styles
```bash
npx stylelint "src/**/*.{css,scss,vue}"
```

### Lint and Fix
```bash
npx stylelint "src/**/*.{css,scss,vue}" --fix
```

### Lint Specific File
```bash
npx stylelint src/components/MyComponent.vue
```

## Integration with package.json Scripts

```json
{
  "scripts": {
    "lint:style": "stylelint 'src/**/*.{css,scss,vue}'",
    "lint:style:fix": "stylelint 'src/**/*.{css,scss,vue}' --fix",
    "lint": "npm run lint:js && npm run lint:style",
    "lint:fix": "npm run lint:js:fix && npm run lint:style:fix"
  }
}
```

## Integration with lint-staged

`.lintstagedrc.json`:
```json
{
  "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
  "*.{css,scss,vue}": ["stylelint --fix", "prettier --write"],
  "*.{json,md,yml,yaml}": ["prettier --write"]
}
```

## Integration with Husky

`.husky/pre-commit`:
```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npm run lint
npx stylelint src/**/*.vue src/**/*.scss
npm run test:unit
npx lint-staged
```

## Alternative Configuration Formats

### .stylelintrc.json (with extension)
```json
{
  "extends": ["stylelint-config-standard-scss"]
}
```

### .stylelintrc.js (JavaScript)
```javascript
module.exports = {
  extends: [
    'stylelint-config-standard-scss',
    'stylelint-config-recommended-vue',
  ],
  rules: {
    'import-notation': 'string',
    // ... other rules
  },
};
```

### stylelint.config.js
```javascript
export default {
  extends: [
    'stylelint-config-standard-scss',
    'stylelint-config-recommended-vue',
  ],
  rules: {
    // ... rules
  },
};
```

### package.json
```json
{
  "stylelint": {
    "extends": ["stylelint-config-standard-scss"]
  }
}
```

## Editor Integration

### VS Code
Install "Stylelint" extension

**.vscode/settings.json:**
```json
{
  "stylelint.enable": true,
  "stylelint.validate": ["css", "scss", "vue"],
  "editor.codeActionsOnSave": {
    "source.fixAll.stylelint": true
  },
  "css.validate": false,
  "scss.validate": false
}
```

### WebStorm/IntelliJ IDEA
1. Settings → Languages & Frameworks → Style Sheets → Stylelint
2. Check "Enable"
3. Configuration file: `.stylelintrc`

## CI/CD Integration

### GitHub Actions
```yaml
name: Lint Styles
on: [push, pull_request]
jobs:
  stylelint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint:style
```

## Vue Component Example

```vue
<template>
  <div class="my-component">
    <h1 class="title">Hello World</h1>
  </div>
</template>

<style scoped lang="scss">
@use '@/theme/' as *;

$component-padding: 20px;

.my-component {
  padding: $component-padding;
  background-color: rgba(0, 0, 0, 0.1);

  .title {
    font-size: 1.5rem;
    color: $primary-color;
    margin-bottom: 1em;
  }

  @media (min-width: 768px) {
    padding: 2rem;
  }
}
</style>
```

## Common Patterns

### SCSS Variables
```scss
// Enforced spacing
$primary-color: #007bff;
$secondary-color: #6c757d;
$font-family: 'Arial', sans-serif;
```

### Mixins
```scss
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

.container {
  @include flex-center;
}
```

### Media Queries
```scss
.element {
  width: 100%;

  @media (min-width: 768px) {
    width: 50%;
  }

  @media (min-width: 1024px) {
    width: 33.333%;
  }
}
```

## Troubleshooting

### Unknown at-rule errors
If using Tailwind or custom at-rules, ensure `"at-rule-no-unknown": null` is set.

### Vue files not being linted
- Install `postcss-html`
- Ensure `.vue` extension in glob patterns
- Check `stylelint-config-recommended-vue` is installed

### SCSS syntax errors
- Verify `stylelint-config-standard-scss` is installed
- Check SCSS syntax is valid
- Ensure file has `.scss` extension

### Vendor prefix warnings
Set `"property-no-vendor-prefix": null` to allow vendor prefixes.

## Best Practices

### 1. Consistent Unit Usage
```scss
// Good - consistent rem usage
font-size: 1rem;
padding: 0.5rem 1rem;

// Avoid mixing units unnecessarily
font-size: 16px;
padding: 8px 1rem;
```

### 2. SCSS Variable Formatting
```scss
// Good
$primary-color: #007bff;

// Bad
$primary-color:#007bff;
```

### 3. Import Organization
```scss
// Variables and functions first
@import "variables";
@import "functions";
@import "mixins";

// Component imports
@import "components/button";
```

### 4. Use Allowed Units
Stick to the allowed unit list for consistency.

### 5. Fix Before Commit
Use Husky and lint-staged to auto-fix styles.

## Ignoring Specific Lines

### Disable for Next Line
```scss
/* stylelint-disable-next-line */
.element {
  color: red;
}
```

### Disable Specific Rule
```scss
/* stylelint-disable-next-line selector-class-pattern */
.MyComponent { }
```

### Disable for Block
```scss
/* stylelint-disable */
.legacy-code {
  color: red;
}
/* stylelint-enable */
```

## Related Files

### .stylelintignore
```
node_modules/
dist/
build/
coverage/
*.min.css
```

## Documentation Template

Add to README.md:

```markdown
## Style Linting

This project uses Stylelint for CSS/SCSS linting.

### Configuration
- SCSS standard rules
- Vue component support
- Allows common units (px, rem, em, %, vh, vw)

### Lint Styles
```bash
npm run lint:style
```

### Fix Style Issues
```bash
npm run lint:style:fix
```

### Editor Setup
Install Stylelint extension and enable auto-fix on save.
```

## Useful Commands

```bash
# Lint all styles
npx stylelint "src/**/*.{css,scss,vue}"

# Lint and fix
npx stylelint "src/**/*.{css,scss,vue}" --fix

# Lint specific file
npx stylelint src/App.vue

# Custom config file
npx stylelint "src/**/*.scss" --config .stylelintrc.custom

# Output JSON
npx stylelint "src/**/*.scss" --formatter json
```

## Resources

- [Stylelint Documentation](https://stylelint.io/)
- [Stylelint Rules](https://stylelint.io/user-guide/rules)
- [SCSS Config](https://github.com/stylelint-scss/stylelint-config-standard-scss)
- [Vue Config](https://github.com/ota-meshi/stylelint-config-recommended-vue)
