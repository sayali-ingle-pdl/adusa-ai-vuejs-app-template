# Prettier Config Skill

## Purpose
Generate Prettier configuration files for code formatting.

## Output
Create the files:
- `.prettierrc.json`
- `.prettierignore`

## Template: .prettierrc.json

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "arrowParens": "avoid",
  "endOfLine": "auto"
}
```

## Template: .prettierignore

```
node_modules
dist
coverage
build
*.min.js
*.min.css
package-lock.json
```

## Notes
- Enforces consistent code style across the project
- Single quotes for strings
- 100 character line width
- ES5 trailing commas for better git diffs
- Ignores generated and build files
