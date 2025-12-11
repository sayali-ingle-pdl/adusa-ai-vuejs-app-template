# Stylelint Config Skill

## Purpose
Generate a stylelint configuration file (`.stylelintrc` or `stylelint.config.cjs`) for Vue 3 projects that enforces consistent SCSS styling conventions while allowing necessary flexibility for component development.

## Skill Parameters

This skill uses the following parameters from the application configuration:

- **Required**: None (uses static configuration)
- **Optional**: 
  - `config_format`: File format for stylelint config (`json` or `cjs`). Default: `cjs`

## Configuration Philosophy

The stylelint configuration balances code quality with developer productivity by:
1. **Standard Foundation**: Extending industry-standard configs for SCSS and Vue
2. **Selective Enforcement**: Enforcing critical rules while disabling overly restrictive ones
3. **Legacy Support**: Allowing legacy CSS syntax where needed for browser compatibility
4. **Unit Flexibility**: Permitting appropriate CSS units for different use cases
5. **SCSS Best Practices**: Enforcing SCSS-specific conventions

## Core Rules Explained

### Import and Notation Rules
- **`import-notation: "string"`**: Enforces string notation for @import statements for consistency
- **`color-function-notation: "legacy"`**: Allows legacy color functions (rgb(), rgba()) for broader compatibility
- **`alpha-value-notation: "number"`**: Uses numeric alpha values (0-1) instead of percentages
- **`media-feature-range-notation: "prefix"`**: Uses prefix notation (min-width) instead of range syntax

### Allowed Units
Permits specific units essential for responsive design:
- **Layout**: `px`, `em`, `rem`, `%`, `fr`
- **Viewport**: `vh`, `vw`
- **Animation**: `s` (seconds)
- **Transforms**: `deg` (degrees)
- **Print**: `in` (inches)

### Disabled Rules (and Why)
- **`property-no-vendor-prefix: null`**: Allows vendor prefixes for cross-browser compatibility
- **`scss/at-extend-no-missing-placeholder: null`**: Permits extending non-placeholder selectors
- **`no-invalid-position-at-import-rule: null`**: Flexible import positioning for modular styles
- **`no-descending-specificity: null`**: Allows natural CSS cascade without warnings
- **`selector-pseudo-element-colon-notation: null`**: Permits both single and double colon notation
- **`declaration-empty-line-before: null`**: No forced spacing between declarations
- **`at-rule-empty-line-before: null`**: Flexible spacing around at-rules
- **`value-keyword-case: null`**: Allows mixed case for CSS keywords
- **`at-rule-no-unknown: null`**: Permits custom at-rules (handled by SCSS parser)
- **`selector-class-pattern: null`**: No enforced naming pattern for classes
- **`declaration-property-value-no-unknown: null`**: Allows unknown property-value combinations

### Enforced SCSS Rules
- **`scss/dollar-variable-colon-space-after: "always"`**: Requires space after colon in SCSS variables for readability

## File Generation

### File Path
- **CommonJS Format**: `stylelint.config.cjs` (recommended for Node.js compatibility)
- **JSON Format**: `.stylelintrc` (simpler but less flexible)

### Dependencies
Add these to `package.json` devDependencies:
- `stylelint` - Core linting engine
- `stylelint-config-standard-scss` - Standard SCSS rules
- `stylelint-config-recommended-vue` - Vue-specific rules
- `postcss-html` - HTML parser for Vue single-file components

**Note**: Always use the latest stable versions at generation time. The configuration structure is designed to remain compatible across versions. If incase any of these dependencies are outdated, install the alternate package and update the configurations accordingly.


## Package.json Scripts

Add these scripts to enable stylelint usage:

```json
{
  "scripts": {
    "lint:style": "stylelint \"**/*.{vue,scss,css}\" --cache",
    "lint:style:fix": "stylelint \"**/*.{vue,scss,css}\" --fix --cache"
  }
}
```


## Migration Notes for Future Versions

When stylelint releases new major versions:

1. **Check Breaking Changes**: Review the migration guide for deprecated rules
2. **Update Extends**: Ensure extended configs support the new version
3. **Test Rules**: Some disabled rules may be renamed or removed
4. **New Rules**: Consider enabling new rules that align with project standards
5. **Deprecation Warnings**: Address any deprecation warnings in the output

### Common Migration Patterns

- **Renamed Rules**: Update rule names to their new equivalents
- **Changed Defaults**: Explicitly set rules that had default value changes
- **Removed Rules**: Remove references to deleted rules
- **New Extends**: Update to new versions of extended configs

## Best Practices

1. **Keep Dependencies Updated**: Regularly update stylelint and config packages
2. **Run in CI/CD**: Include stylelint checks in your CI pipeline
3. **Pre-commit Hooks**: Use Husky to run stylelint before commits
4. **Team Alignment**: Discuss rule changes with the team before implementing
5. **Documentation**: Document custom rules and their rationale

## Related Skills

- **ESLint Config Skill**: For JavaScript/TypeScript linting
- **Prettier Config Skill**: For code formatting
- **Husky Skill**: For pre-commit hooks
- **Theme Skill**: For SCSS architecture that this linter will validate
