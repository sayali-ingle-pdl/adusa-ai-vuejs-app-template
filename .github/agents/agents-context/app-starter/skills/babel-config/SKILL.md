# Babel Config Skill

## Purpose
Generate the `babel.config.cjs` file for transpilation in tests.

## Output
Create the file: `babel.config.cjs`


## When to Use Babel

### ✅ Use Babel For:
- Jest test transpilation
- Supporting older Node.js versions
- Custom transformations
- Legacy browser support

### ❌ Don't Use Babel For:
- Vite production builds (use esbuild/Vite)
- Modern browser-only apps (use native ES modules)
- Simple projects without tests

## Babel in Vue/Vite Projects

### Development & Production
- **Vite** handles transpilation
- Fast with esbuild
- No Babel needed

### Testing (Jest)
- **Babel** transforms test files
- Jest requires transpilation
- `babel.config.cjs` necessary


## Notes
- Simple Babel configuration for Jest and test environments
- Transpiles modern JavaScript for Node test environment
- Uses CommonJS format (.cjs) for compatibility
- `@babel/preset-env` automatically determines necessary transformations
- Enables use of modern syntax in test files
- Required for Jest to process ES modules and modern JavaScript features
