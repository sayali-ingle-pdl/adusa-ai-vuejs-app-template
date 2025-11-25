# Prettier Config Skill

## Purpose
Generate Prettier configuration file for code formatting.

## Output
Create the file: `.prettierrc`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

## Notes
- Enforces consistent code style across the project
- Uses single quotes for strings
- Requires semicolons at end of statements
- ES5 trailing commas for better git diffs and version control
- Minimal configuration using Prettier's sensible defaults
- Works with ESLint and other code quality tools
