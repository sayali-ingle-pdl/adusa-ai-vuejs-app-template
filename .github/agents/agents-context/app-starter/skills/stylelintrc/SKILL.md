# Stylelint Configuration Skill

## Purpose
Generate Stylelint configuration for linting CSS, SCSS, and Vue component styles.

## Output
Create the file: `.stylelintrc`


## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- Extends standard SCSS and Vue-specific configurations
- Allows common CSS units (px, rem, em, %, vh, vw, etc.)
- Disables overly strict rules for better developer experience
- Enforces spacing after SCSS variable colons
- Compatible with Vue single-file components
- Works with preprocessors like SCSS/Sass
- Integrates with lint-staged and Husky for pre-commit checks
