# Babel Config Skill

## Purpose
Generate the `babel.config.cjs` file for transpilation in tests.

## Output
Create the file: `babel.config.cjs`

## Template

```javascript
module.exports = {
  presets: ['@babel/preset-env'],
};
```

## Notes
- Simple Babel configuration for Jest
- Transpiles modern JavaScript for Node test environment
