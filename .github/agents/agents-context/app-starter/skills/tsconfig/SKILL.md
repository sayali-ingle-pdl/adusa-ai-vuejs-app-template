# TypeScript Config Skill

## Purpose
Generate the `tsconfig.json` file for TypeScript compilation configuration in a Vue 3 + Vite application.

## Output
Create the file: `tsconfig.json`

## Configuration Requirements

### Compiler Options

#### Language and Module Settings
- **Target Environment**: Use the latest ECMAScript standard to leverage modern JavaScript features
- **Module System**: Configure for ESNext modules to support modern bundlers and tree-shaking
- **JSX Handling**: Preserve JSX syntax (let Vite/Babel handle transformation)
- **Module Resolution**: Use Node.js-style module resolution for compatibility with npm packages

#### Type Checking (Strict Mode)
- **Enable Strict Mode**: All strict type-checking options should be enabled for maximum type safety
- **Force Consistent Casing**: Enforce consistent file name casing to prevent cross-platform issues

#### Modern JavaScript Features
- **Experimental Decorators**: Enable decorator support for Vue class components and metadata
- **Define for Class Fields**: Use standard ECMAScript semantics for class field initialization
- **Resolve JSON Modules**: Allow importing JSON files as modules

#### Interoperability
- **ES Module Interop**: Enable interoperability between CommonJS and ES modules
- **Synthetic Default Imports**: Allow default imports from modules with no default export
- **Skip Lib Check**: Skip type checking of declaration files for faster compilation

#### Development Experience
- **Source Maps**: Generate source maps for debugging TypeScript in browser dev tools
- **Base URL**: Set to "." (project root) to enable path aliasing

#### Type Definitions
- **Required Types**: Include type definitions for:
  - Your chosen test framework (Jest, Vitest, etc.)
  - Vite client types (for `import.meta.env` and other Vite features)
  
#### Library Support
- **DOM APIs**: Include latest ECMAScript library, DOM, DOM Iterable, and ScriptHost types

#### Path Aliases
- **`@/` Alias**: Map `@/*` to `src/*` for cleaner imports throughout the application

### File Inclusion

#### Include Patterns
Include all TypeScript and Vue files in:
- Source directory (`src/**/*.ts`, `src/**/*.tsx`, `src/**/*.vue`)
- Test directory (match your test file location, e.g., `tests/**/*.ts`)
- Type declaration files (e.g., `src/shims-svg.d.ts`, `src/shims-vue.d.ts`)

#### Exclude Patterns
Exclude:
- `node_modules` directory
- Test spec files from main compilation (e.g., `src/**/*.spec.ts`)

## Integration Notes

### Testing Framework Integration
- If using **Jest**: Include `"jest"` in the types array
- If using **Vitest**: Include `"vitest/globals"` in the types array (if using globals)

### Vue 3 Support
- Ensure JSX is preserved for Vue's compiler to handle
- Include `.vue` files in the compilation
- May require `vue-tsc` for Vue component type checking

### Vite Integration
- Include `"vite/client"` types for Vite-specific features
- Ensure module resolution works with Vite's expectations
- Source maps should be enabled for development

## Validation

After generating the configuration:
1. Verify TypeScript can resolve imports using the `@/` alias
2. Check that Vue component imports don't show type errors
3. Confirm test files can access test framework globals/types
4. Ensure `import.meta.env` doesn't show type errors in source files

## Philosophy

This configuration prioritizes:
- **Type Safety**: Strict mode catches potential bugs at compile time
- **Modern Standards**: Uses latest ECMAScript features for better code quality
- **Developer Experience**: Path aliases and source maps improve development workflow
- **Build Tool Integration**: Works seamlessly with Vite and modern bundlers
- **Testing Support**: Properly configured for testing frameworks

## Future Compatibility

When TypeScript evolves:
- Replace deprecated options with their modern equivalents
- Adopt new strict flags as they're introduced for enhanced type safety
- Update target/lib versions to leverage new ECMAScript features
- Maintain the core principles: strict typing, modern modules, and proper path resolution