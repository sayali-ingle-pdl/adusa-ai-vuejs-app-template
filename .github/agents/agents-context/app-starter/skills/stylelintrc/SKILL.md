# Stylelint Configuration Skill

## Purpose
Generate Stylelint configuration for linting CSS, SCSS, and Vue component styles.

## Output
Create the file: `.stylelintrc`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

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

## Notes
- Extends standard SCSS and Vue-specific configurations
- Allows common CSS units (px, rem, em, %, vh, vw, etc.)
- Disables overly strict rules for better developer experience
- Enforces spacing after SCSS variable colons
- Compatible with Vue single-file components
- Works with preprocessors like SCSS/Sass
- Integrates with lint-staged and Husky for pre-commit checks
