# Babel Config Skill

## Purpose
Generate the `babel.config.cjs` file for transpilation in tests.

## Output
Create the file: `babel.config.cjs`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```javascript
module.exports = {
  presets: ['@babel/preset-env'],
};
```

## Notes
- Simple Babel configuration for Jest and test environments
- Transpiles modern JavaScript (ES6+) for Node test environment
- Uses CommonJS format (.cjs) for compatibility
- `@babel/preset-env` automatically determines necessary transformations
- Enables use of modern syntax in test files
- Required for Jest to process ES modules and modern JavaScript features
