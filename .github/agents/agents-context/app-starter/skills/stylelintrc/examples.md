# Stylelint Configuration Examples

## ⚠️ CRITICAL DEPRECATION WARNING

**Date**: December 2025
**Status**: This example may be outdated

**🚨 HIGH PRIORITY - DO NOT USE WITHOUT VERIFICATION**

Before using this example:
1. ✅ **Verify latest Stylelint version**: Run `npm view stylelint version`
2. ✅ **Check config package versions**: Verify `stylelint-config-standard-scss` and `stylelint-config-recommended-vue` are current
3. ✅ **Verify rule compatibility**: Check https://stylelint.io/user-guide/rules/ for deprecated rules
4. ✅ **Detect stylesheet type**: Determine if project uses SCSS or CSS
5. ✅ **Check format support**: Verify `.stylelintrc` is still supported (may need `.stylelintrc.json`)
6. ✅ **Lint-staged integration**: Check if stylelint should be added to pre-commit hooks

**If Stylelint >= 16.0.0**:
- **Breaking changes**: Plugin system changed, verify all configs are compatible
- **Rule changes**: Some rules deprecated or renamed
- **Check migration guide**: https://stylelint.io/migration-guide/to-16

---

## Example 1: .stylelintrc (SCSS + Vue) - Recommended

**Configuration Date**: December 2025
**Stylelint Version**: 16.x
**Format**: JSON
**Use Case**: Vue 3 projects with SCSS preprocessing

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

---

## Example 2: .stylelintrc (CSS + Vue) - Plain CSS Projects

**Configuration Date**: December 2025
**Stylelint Version**: 16.x
**Format**: JSON
**Use Case**: Vue 3 projects with plain CSS (no preprocessor)

```json
{
  "extends": [
    "stylelint-config-standard",
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
    "no-invalid-position-at-import-rule": null,
    "no-descending-specificity": null,
    "selector-pseudo-element-colon-notation": null,
    "declaration-empty-line-before": null,
    "at-rule-empty-line-before": null,
    "value-keyword-case": null,
    "at-rule-no-unknown": null,
    "selector-class-pattern": null,
    "declaration-property-value-no-unknown": null
  }
}
```

**Difference from SCSS version**:
- ❌ Removed: `stylelint-config-standard-scss` (SCSS-specific)
- ✅ Added: `stylelint-config-standard` (plain CSS)
- ❌ Removed: SCSS-specific rules (`scss/at-extend-no-missing-placeholder`, `scss/dollar-variable-colon-space-after`)

---

## Rule Explanations

### Extended Configurations

**`stylelint-config-standard-scss`** (for SCSS projects)
- Provides standard SCSS linting rules
- Includes SCSS-specific syntax support (@use, @forward, @mixin, etc.)
- **Why**: Comprehensive SCSS best practices from the community
- **Verify**: Run `npm view stylelint-config-standard-scss version`
- **Compatibility**: Requires Stylelint 16.0.0+

**`stylelint-config-standard`** (for CSS projects)
- Provides standard CSS linting rules
- Modern CSS features support (custom properties, grid, etc.)
- **Why**: Comprehensive CSS best practices from the community
- **Verify**: Run `npm view stylelint-config-standard version`
- **Compatibility**: Requires Stylelint 16.0.0+

**`stylelint-config-recommended-vue`**
- Vue single-file component (SFC) support
- Lints `<style>` blocks in `.vue` files
- **Why**: Essential for Vue component style linting
- **Verify**: Run `npm view stylelint-config-recommended-vue version`
- **Compatibility**: Works with both CSS and SCSS

### Rule Overrides (Why Each Rule is Configured)

#### Import and Module Rules

**`"import-notation": "string"`**
- Forces `@use "file"` instead of `@use 'file'`
- **Why**: Consistency in import statements
- **Values**: `"string"` | `"url"`
- **Affects**: SCSS `@use`, `@import`, `@forward`

**`"no-invalid-position-at-import-rule": null`**
- Disables errors for `@import` position
- **Why**: SCSS `@use` rules can be flexible in order
- **Default**: Error if `@import` not at top
- **Disabled**: Too strict for real-world SCSS modules

#### Color and Value Rules

**`"color-function-notation": "legacy"`**
- Allows `rgb(255, 255, 255)` instead of `rgb(255 255 255)`
- **Why**: Legacy syntax more readable and widely used
- **Values**: `"modern"` | `"legacy"`
- **Affects**: `rgb()`, `rgba()`, `hsl()`, `hsla()`
- **⚠️ Verify**: Check if Stylelint 16+ changed default

**`"alpha-value-notation": "number"`**
- Allows `rgba(0, 0, 0, 0.5)` instead of `rgba(0, 0, 0, 50%)`
- **Why**: Decimal notation more common and intuitive
- **Values**: `"number"` | `"percentage"`
- **Affects**: Opacity/alpha values in color functions

**`"value-keyword-case": null`**
- Disables case enforcement for CSS keywords
- **Why**: Allow both `currentColor` and `currentcolor`
- **Default**: Enforces lowercase
- **Disabled**: Too strict, both are valid

#### Vendor Prefix Rules

**`"property-no-vendor-prefix": null`**
- Allows `-webkit-`, `-moz-`, `-ms-` prefixes
- **Why**: Sometimes required for browser compatibility
- **Default**: Error on vendor prefixes
- **Disabled**: Autoprefixer not always used, prefixes still needed

#### Media Query Rules

**`"media-feature-range-notation": "prefix"`**
- Allows `(min-width: 768px)` instead of `(width >= 768px)`
- **Why**: Classic syntax more familiar and widely supported
- **Values**: `"prefix"` | `"context"`
- **Affects**: Media query range syntax

#### Unit Rules

**`"unit-allowed-list": [...]`**
- Whitelist of allowed CSS units
- **Why**: Prevent typos like `pz` or uncommon units
- **Allowed**: `em`, `rem`, `%`, `px`, `s`, `in`, `deg`, `fr`, `vh`, `vw`
- **Common use cases**:
  - `px`, `rem`, `em` - Sizing
  - `%` - Percentages
  - `vh`, `vw` - Viewport units
  - `deg` - Rotations
  - `fr` - Grid fractions
  - `s` - Transitions/animations
  - `in` - Print styles

#### Specificity and Selector Rules

**`"no-descending-specificity": null`**
- Disables error for decreasing specificity order
- **Why**: Too strict for component-based CSS
- **Default**: Error if less specific selector comes after more specific
- **Disabled**: Real-world CSS often requires flexible specificity

**`"selector-class-pattern": null`**
- Disables class naming pattern enforcement
- **Why**: Allow BEM, camelCase, kebab-case, etc.
- **Default**: Enforces kebab-case
- **Disabled**: Projects use various naming conventions

**`"selector-pseudo-element-colon-notation": null`**
- Allows both `::before` and `:before`
- **Why**: Legacy syntax still valid
- **Default**: Enforces double colon `::`
- **Disabled**: Both syntaxes widely used

#### At-Rule Rules

**`"at-rule-no-unknown": null`**
- Disables errors for unknown at-rules
- **Why**: SCSS has custom at-rules, PostCSS plugins add more
- **Default**: Error on unrecognized at-rules
- **Disabled**: Prevents false positives with SCSS/PostCSS

**`"at-rule-empty-line-before": null`**
- Disables empty line requirement before at-rules
- **Why**: Flexible formatting preferences
- **Default**: Requires empty line before at-rules
- **Disabled**: Too strict for compact styles

#### SCSS-Specific Rules (SCSS projects only)

**`"scss/at-extend-no-missing-placeholder": null`**
- Allows `@extend .class` without placeholder `%`
- **Why**: Sometimes extending classes is necessary
- **Default**: Error if extending non-placeholder
- **Disabled**: Too strict for practical SCSS usage

**`"scss/dollar-variable-colon-space-after": "always"`**
- Enforces space after colon in SCSS variables
- **Why**: Consistency and readability
- **Format**: `$color: red` not `$color:red`
- **Values**: `"always"` | `"never"` | `"at-least-one-space"`

#### Declaration Rules

**`"declaration-empty-line-before": null`**
- Disables empty line requirement before declarations
- **Why**: Allow compact property groups
- **Default**: Requires empty line before declarations (in some cases)
- **Disabled**: Too strict for real-world CSS

**`"declaration-property-value-no-unknown": null`**
- Disables errors for unknown property values
- **Why**: CSS evolves, new values added frequently
- **Default**: Error on unrecognized property values
- **Disabled**: Prevents false positives for new CSS features

---

## How to Detect Stylesheet Type

### Method 1: Check package.json Dependencies

```bash
# Check for SCSS preprocessor
cat package.json | grep -E '"(sass|node-sass)"'

# If found → Use SCSS config
# If not found → Check files (Method 2)
```

**SCSS indicators**:
- `"sass": "^1.x.x"` - Modern Sass (Dart Sass)
- `"node-sass": "^x.x.x"` - Legacy Sass (deprecated, but still used)

### Method 2: Check Project Files

```bash
# Search for SCSS files
find src -name "*.scss" -type f | head -n 1

# If SCSS files found → Use SCSS config
# If only CSS files → Use CSS config
```

### Method 3: Check Vite/Vue Config

```bash
# Check vite.config.ts for preprocessor options
grep -q "preprocessorOptions" vite.config.ts

# Check for SCSS in Vue SFCs
grep -q '<style.*lang="scss"' src/**/*.vue
```

### Decision Matrix

| Condition | Configuration |
|-----------|---------------|
| `sass` or `node-sass` in package.json | SCSS config |
| `*.scss` files in `src/` | SCSS config |
| `lang="scss"` in Vue components | SCSS config |
| Only `*.css` files | CSS config |
| No styles found | Default to SCSS (Vue projects commonly use SCSS) |

---

## Lint-Staged Integration

### Adding Stylelint to .lintstagedrc

**Recommended pattern**:
```json
{
  "**/*.{css,scss,vue}": "stylelint --fix"
}
```

**Complete .lintstagedrc example**:
```json
{
  "**/*.{js,ts,vue}": "eslint --fix",
  "**/*.{css,scss,vue}": "stylelint --fix",
  "**/*.{js,ts,vue,css,scss,json,md}": "prettier --write"
}
```

**Why integrate**:
- ✅ Auto-fix style issues before commit
- ✅ Catch linting errors early
- ✅ Maintain code quality consistency
- ✅ Only lint changed files (performance)

**Cross-check**: If `.lintstagedrc` exists but missing stylelint:
```bash
# Check if stylelint in lint-staged
grep -q "stylelint" .lintstagedrc || echo "⚠️ Add stylelint to lint-staged"
```

---

## Version Verification Commands

### Check Current Versions

**Stylelint**:
```bash
# Latest version
npm view stylelint version

# Installed version
npm list stylelint

# Check for updates
npm outdated stylelint
```

**Config Packages (SCSS)**:
```bash
# Latest versions
npm view stylelint-config-standard-scss version
npm view stylelint-config-recommended-vue version

# Installed versions
npm list stylelint-config-standard-scss stylelint-config-recommended-vue

# Check for updates
npm outdated stylelint-config-standard-scss stylelint-config-recommended-vue
```

**Config Packages (CSS)**:
```bash
# Latest versions
npm view stylelint-config-standard version
npm view stylelint-config-recommended-vue version

# Installed versions
npm list stylelint-config-standard stylelint-config-recommended-vue
```

### Check Rule Compatibility

**Verify rule exists**:
```bash
# Search Stylelint documentation
open https://stylelint.io/user-guide/rules/

# Check if rule deprecated
npm view stylelint changelog | grep "unit-allowed-list"
```

**Test configuration**:
```bash
# Validate config file
npx stylelint --print-config .stylelintrc

# Run stylelint on test file
npx stylelint "src/**/*.{css,scss,vue}"

# Auto-fix issues
npx stylelint "src/**/*.{css,scss,vue}" --fix
```

---

## Alternative Configuration Formats

### .stylelintrc.json (Explicit JSON)

```json
{
  "extends": [
    "stylelint-config-standard-scss",
    "stylelint-config-recommended-vue"
  ],
  "rules": {
    "import-notation": "string"
  }
}
```

### .stylelintrc.cjs (CommonJS)

```javascript
module.exports = {
  extends: [
    'stylelint-config-standard-scss',
    'stylelint-config-recommended-vue',
  ],
  rules: {
    'import-notation': 'string',
    'color-function-notation': 'legacy',
    // ... rest of rules
  },
};
```

### .stylelintrc.js (ES Module)

```javascript
export default {
  extends: [
    'stylelint-config-standard-scss',
    'stylelint-config-recommended-vue',
  ],
  rules: {
    'import-notation': 'string',
    'color-function-notation': 'legacy',
    // ... rest of rules
  },
};
```

### stylelint.config.cjs (Alternative Name)

```javascript
module.exports = {
  extends: [
    'stylelint-config-standard-scss',
    'stylelint-config-recommended-vue',
  ],
  rules: {
    // ... rules
  },
};
```

### package.json Field

```json
{
  "stylelint": {
    "extends": [
      "stylelint-config-standard-scss",
      "stylelint-config-recommended-vue"
    ],
    "rules": {
      "import-notation": "string"
    }
  }
}
```

**Format Recommendation**:
- **Primary**: `.stylelintrc` (JSON) - simple, no extra dependencies
- **Alternative**: `.stylelintrc.cjs` - if programmatic config needed
- **Verify**: Check Stylelint docs for supported formats

---

## Common Issues and Solutions

**Issue**: Stylelint fails to parse SCSS syntax
- **Solution**: Ensure `stylelint-config-standard-scss` is installed and extended
- **Check**: Verify `sass` package is in dependencies

**Issue**: Vue SFC styles not linted
- **Solution**: Ensure `stylelint-config-recommended-vue` is in extends
- **Check**: Verify `.vue` files included in lint pattern

**Issue**: "Unknown rule" errors
- **Solution**: Check rule name hasn't changed in Stylelint 16+
- **Action**: Visit https://stylelint.io/migration-guide/to-16
- **Fix**: Update or remove deprecated rules

**Issue**: Vendor prefix warnings despite `null` setting
- **Solution**: Clear cache, restart editor
- **Check**: Verify rule override is in correct config file

**Issue**: `unit-allowed-list` not working
- **Solution**: Verify Stylelint version supports this rule
- **Check**: May be deprecated in future versions
- **Alternative**: Use `unit-disallowed-list` for blocklist approach

**Issue**: SCSS variables flagged incorrectly
- **Solution**: Ensure using SCSS config, not plain CSS config
- **Check**: Verify `stylelint-config-standard-scss` in extends

**Issue**: Stylelint conflicts with Prettier
- **Solution**: Run Stylelint before Prettier in lint-staged
- **Order**: ESLint → Stylelint → Prettier
- **Pattern**: Handle formatting last

**Issue**: Performance issues with large projects
- **Solution**: Use `.stylelintignore` to exclude files
- **Ignore**: `dist/`, `node_modules/`, `coverage/`, `*.min.css`
- **Optimize**: Only lint changed files with lint-staged

---

## .stylelintignore Example

```
# Dependencies
node_modules/

# Build outputs
dist/
build/
.output/

# Test coverage
coverage/

# Minified files
*.min.css

# Third-party
vendor/
```

---

## Testing Configuration

### Validate Config

```bash
# Check config is valid
npx stylelint --print-config .stylelintrc

# Should output resolved config without errors
```

### Test on Sample File

```bash
# Create test SCSS file
echo '$color: red;.test{color:$color}' > test.scss

# Run stylelint
npx stylelint test.scss

# Should show warnings/errors based on rules
```

### Run Full Project Lint

```bash
# Lint all style files
npx stylelint "src/**/*.{css,scss,vue}"

# With auto-fix
npx stylelint "src/**/*.{css,scss,vue}" --fix

# Output to file
npx stylelint "src/**/*.{css,scss,vue}" --output-file stylelint-report.txt
```

---

## Migration from Stylelint 15 to 16

### Breaking Changes (Verify Current Status)

**Plugin system changed**:
- Old: Custom plugins loaded differently
- New: Updated plugin API
- **Action**: Check all config packages are Stylelint 16 compatible

**Rule changes**:
- Some rules renamed or deprecated
- **Action**: Review https://stylelint.io/migration-guide/to-16

**Config format**:
- Extends resolution changed
- **Action**: Test config loads correctly

### Migration Steps

1. **Update Stylelint**:
   ```bash
   npm install -D stylelint@latest
   ```

2. **Update Config Packages**:
   ```bash
   npm install -D stylelint-config-standard-scss@latest
   npm install -D stylelint-config-recommended-vue@latest
   ```

3. **Test Configuration**:
   ```bash
   npx stylelint --print-config .stylelintrc
   ```

4. **Check for Deprecated Rules**:
   ```bash
   npx stylelint "src/**/*.{css,scss,vue}"
   # Review warnings about deprecated rules
   ```

5. **Update Rules**:
   - Remove deprecated rules
   - Replace with new equivalents
   - Test linting works correctly

---

## Important Reminders

1. **Always Research First**: Verify Stylelint and config package versions before using
2. **Detect Stylesheet Type**: Check for SCSS vs CSS to use correct config
3. **Latest Versions**: Fetch current versions from npm registry
4. **Lint-Staged Integration**: Add stylelint to pre-commit hooks if lint-staged exists
5. **Format Flexibility**: Be ready to switch config format if deprecated
6. **Rule Compatibility**: Check for deprecated rules in new Stylelint versions
7. **Vue SFC Support**: Always include `stylelint-config-recommended-vue` for Vue projects
8. **Cross-Skill Coordination**: Check if lintstagedrc skill already added stylelint pattern
9. **Performance**: Use `.stylelintignore` and lint-staged for large projects
10. **Testing**: Validate config and test on sample files before committing

