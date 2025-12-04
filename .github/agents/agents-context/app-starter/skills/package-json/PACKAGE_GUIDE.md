# Package.json Developer Guide

Comprehensive guide to understanding package.json configuration for Vue 3 + Vite applications.

## Table of Contents

- [Package Metadata](#package-metadata)
- [Scripts Explained](#scripts-explained)
- [Dependencies Guide](#dependencies-guide)
- [Development Workflow](#development-workflow)
- [Version Management](#version-management)
- [Troubleshooting](#troubleshooting)

## Package Metadata

### Name Field
```json
"name": "@pdl-fulfillment-omni/omni-inventory-manager-web"
```

**Format**: `@scope/package-name`
- **Scope**: Organization namespace (e.g., `@pdl-fulfillment-omni`)
- **Package**: Application name in kebab-case
- **Purpose**: NPM registry identification, package imports

### Version Field
```json
"version": "0.1.0"
```

**Semantic Versioning**: `MAJOR.MINOR.PATCH`
- `0.1.0`: Initial development version
- Increment rules:
  - MAJOR: Breaking changes
  - MINOR: New features (backwards compatible)
  - PATCH: Bug fixes

### Private Field
```json
"private": true
```

**Purpose**: Prevents accidental publication to NPM registry
- Always set to `true` for internal applications
- Protects proprietary code

### Type Field
```json
"type": "module"
```

**Purpose**: Enable ES modules (import/export syntax)
- Required for Vite and modern tooling
- Not CommonJS (require/module.exports)

## Scripts Explained

### Development Scripts

#### `dev` - Preview Production Build
```json
"dev": "vite preview --port 8089"
```

**What it does:**
- Runs Vite preview server
- Serves production build locally from `dist/`
- Tests production build before deployment
- No Hot Module Replacement (HMR)

**When to use:**
- Verify production build works correctly
- Test performance of optimized build
- Debug production-specific issues

**Run command:**
```bash
npm run dev
```

---

#### `serve` - Full Development Workflow
```json
"serve": "npm-run-all --parallel build:watch dev lint:init:watch"
```

**What happens:**
1. **build:watch**: Continuously rebuilds on file changes
2. **dev**: Serves built files from `dist/`
3. **lint:init:watch**: Lints code initially and on changes

**When to use:**
- Developing micro-frontend with single-spa
- Testing production build integration
- Mirroring production environment locally

**Run command:**
```bash
npm run serve
```

---

#### `build:watch` - Continuous Build
```json
"build:watch": "vite build --watch"
```

**What it does:**
- Performs initial production build
- Watches for file changes
- Automatically rebuilds on save
- Outputs to `dist/` directory

**When to use:**
- Used automatically by `serve` script
- Testing build process during development

---

#### `serve:standalone` - Standalone Development
```json
"serve:standalone": "vite serve --port 8089 --mode standalone"
```

**What it does:**
- Runs Vite dev server with HMR
- Uses `.env.standalone` configuration
- Sets `STANDALONE_SINGLE_SPA=true`
- Bypasses single-spa launcher

**When to use:**
- Fastest development iteration
- Developing components in isolation
- Testing without micro-frontend complexity

**Run command:**
```bash
npm run serve:standalone
```

---

### Build Scripts

#### `build` - Production Build
```json
"build": "vite build"
```

**What it does:**
- Compiles TypeScript to JavaScript
- Bundles all modules
- Processes Vue Single File Components
- Minifies and optimizes code
- Outputs to `dist/` directory
- Creates SystemJS bundle (for single-spa)

**Output structure:**
```
dist/
├── index.html
├── assets/
│   ├── app.[hash].js
│   └── index.[hash].css
└── ...
```

**Run command:**
```bash
npm run build
```

---

### Testing Scripts

#### `test:unit` - Run Tests Once
```json
"test:unit": "jest"
```

**What it does:**
- Runs all `*.spec.ts` files
- Collects code coverage
- Outputs test results
- Exits with error code if tests fail

**Coverage output:** `coverage/` directory

**Run command:**
```bash
npm run test:unit
```

---

#### `test:unit:watch` - Interactive Testing
```json
"test:unit:watch": "jest --watch"
```

**What it does:**
- Runs tests in watch mode
- Re-runs tests on file changes
- Shows only related tests
- Interactive CLI for filtering tests
- Faster iteration (no coverage by default)

**When to use:**
- Test-driven development (TDD)
- Debugging failing tests
- Continuous test feedback

**Run command:**
```bash
npm run test:unit:watch
```

---

### Linting Scripts

#### `lint` - Check Code Quality
```json
"lint": "eslint --ext .js,.ts,.vue src"
```

**What it does:**
- Lints JavaScript, TypeScript, and Vue files
- Only checks `src/` directory
- Uses `eslint.config.cjs` configuration
- Reports errors and warnings
- Exits with error if issues found

**Run command:**
```bash
npm run lint
```

---

#### `lint:watch` - Auto-lint on Changes
```json
"lint:watch": "chokidar \"src/**/*.{js,ts,vue}\" -i node_modules -i dist -i .git --debounce 500 --initial false -c \"npm run lint\""
```

**Parameters explained:**
- **Watch pattern**: `src/**/*.{js,ts,vue}`
- **Ignores**: node_modules, dist, .git
- **Debounce**: 500ms (waits for typing to stop)
- **Initial**: false (doesn't run on start)
- **Command**: Executes `npm run lint`

**When to use:**
- Get instant feedback while coding
- Catch issues immediately after typing

---

#### `lint:init:watch` - Initial Lint + Watch
```json
"lint:init:watch": "npm run lint && npm run lint:watch"
```

**What it does:**
1. Runs lint once immediately (ensures clean start)
2. Then starts watch mode
3. Continues linting on file changes

**When to use:**
- Used by `serve` script
- Ensures code is clean before starting development

---

### Formatting Scripts

#### `prettier` - Format All Files
```json
"prettier": "prettier --write ."
```

**What it does:**
- Formats all files in project
- Overwrites files with formatted version
- Uses `.prettierrc` configuration
- Respects `.prettierignore` exclusions

**When to use:**
- One-time formatting pass
- Before committing changes
- After major refactoring

**Run command:**
```bash
npm run prettier
```

---

#### `prettier-watch` - Auto-format on Save
```json
"prettier-watch": "onchange \"**/*\" \"!dist/**\" \"!node_modules/**\" -- prettier --write {{changed}}"
```

**What it does:**
- Watches all files for changes
- Excludes dist and node_modules
- Formats only changed files
- Uses `onchange` package

**When to use:**
- Alternative to editor format-on-save
- Ensure team-wide consistency
- Continuous formatting

---

### Git Hooks

#### `prepare` - Husky Initialization
```json
"prepare": "husky"
```

**What it does:**
- Runs automatically after `npm install`
- Initializes Husky git hooks
- Installs pre-commit hooks
- Sets up lint-staged

**When triggered:**
- `npm install`
- `npm ci`
- After cloning repository

**Purpose:**
- Ensures git hooks are installed for all developers
- Automates quality checks before commits

---

## Dependencies Guide

### Core Framework Dependencies

#### Vue Ecosystem
```json
"vue": "^3.5.13",
"vue-router": "^4.1.6",
"vuex": "^4.0.0",
"vue-class-component": "^8.0.0-0"
```

**Vue 3.5.13**
- Main framework with Composition API
- TypeScript support
- Reactivity system

**Vue Router 4.1.6**
- Official routing library
- Client-side routing
- History mode support
- Route guards

**Vuex 4.0.0**
- State management
- Centralized store
- Actions, mutations, getters
- Vue 3 compatible

**Vue Class Component**
- Class-style component syntax
- TypeScript decorators
- Alternative to Composition API

---

#### Single-spa Integration
```json
"single-spa-vue": "^2.1.0"
```

**Purpose**: Micro-frontend integration
- Single-spa lifecycle adapter
- Mounts/unmounts Vue apps
- Required for micro-frontend architecture

---

#### HTTP Client
```json
"axios": "^1.4.0"
```

**Features:**
- Promise-based HTTP client
- Request/response interceptors
- Automatic transforms
- Cancel requests
- CSRF protection

---

#### Polyfills
```json
"core-js": "^3.8.3"
```

**Purpose**: JavaScript polyfills for older browsers
- Modern features in legacy browsers
- Automatic polyfill injection via Babel
- Tree-shakeable

---

#### Date Utilities
```json
"date-fns": "^2.30.0",
"date-fns-tz": "^2.0.0"
```

**date-fns**: Modern date manipulation
- Lightweight alternative to Moment.js
- Functional, immutable
- Tree-shakeable

**date-fns-tz**: Timezone support
- IANA timezone database
- Timezone conversions
- DST handling

---

#### UI Components
```json
"vue-tippy": "^6.7.1",
"@royalaholddelhaize/pdl-spectrum-component-library-web": "^1.0.3"
```

**vue-tippy**: Tooltip component
- Based on Tippy.js
- Accessible tooltips
- Customizable

**PDL Spectrum**: Internal component library
- Shared UI components
- Consistent design system
- Private GitHub package

---

#### Monitoring
```json
"@datadog/browser-rum": "^5.23.3"
```

**Purpose**: Real User Monitoring (RUM)
- Performance tracking
- Error monitoring
- User session recording
- Custom events

---

### Development Dependencies

#### Build Tools
```json
"vite": "^6.3.5",
"@vitejs/plugin-vue": "^5.2.3",
"vite-plugin-css-injected-by-js": "^3.5.2",
"vite-svg-loader": "^5.1.0"
```

**Vite**: Next-generation build tool
- Lightning-fast HMR
- Native ES modules
- Optimized production builds

**@vitejs/plugin-vue**: Vue SFC support
- Compiles `.vue` files
- Hot module replacement
- Template compilation

**vite-plugin-css-injected-by-js**: CSS injection
- Required for single-spa CSS isolation
- Prevents style conflicts between micro-frontends
- Injects CSS via JavaScript

**vite-svg-loader**: SVG as components
- Import SVG as Vue components
- Inline SVG support
- Optimized output

---

#### TypeScript
```json
"typescript": "4.9",
"@types/node": "^20.17.30",
"@types/jest": "^27.0.1",
"@types/jsdom": "^21.1.1"
```

**TypeScript 4.9**: Exact version
- Type checking
- Pinned for stability
- Avoids TS 5.x breaking changes

**Type definitions**: TypeScript types for libraries

---

#### Testing
```json
"jest": "^27.0.5",
"ts-jest": "^27.0.4",
"@vue/vue3-jest": "^27.0.0-alpha.1",
"babel-jest": "^27.0.6",
"@vue/test-utils": "^2.0.0-0"
```

**Jest**: Testing framework
- Unit tests
- Snapshot testing
- Code coverage
- Mocking utilities

**ts-jest**: TypeScript preprocessor
- Compiles TS for tests
- Type checking in tests

**@vue/vue3-jest**: Vue SFC transformer
- Compiles `.vue` files for tests
- Template/script/style processing

**babel-jest**: JavaScript transformer
- Handles modern JavaScript syntax

**@vue/test-utils**: Vue testing utilities
- Component mounting
- Wrapper API
- Async utilities

---

#### Linting
```json
"eslint": "^9.30.1",
"eslint-plugin-vue": "^9.33.0",
"@typescript-eslint/eslint-plugin": "^8.35.1",
"@typescript-eslint/parser": "^8.35.1",
"vue-eslint-parser": "^10.2.0"
```

**ESLint 9**: Flat config format
- Code quality checks
- Customizable rules
- Plugin ecosystem

**eslint-plugin-vue**: Vue-specific rules
- Template linting
- Vue best practices
- Component conventions

**@typescript-eslint**: TypeScript linting
- Type-aware rules
- TS-specific checks

---

#### Formatting
```json
"prettier": "^3.6.2",
"eslint-config-prettier": "^10.1.8",
"eslint-plugin-prettier": "^5.5.4"
```

**Prettier**: Code formatter
- Opinionated styling
- Consistent formatting
- Multiple language support

**eslint-config-prettier**: Disables conflicting ESLint rules

**eslint-plugin-prettier**: Runs Prettier as ESLint rule

---

#### Styling
```json
"sass": "^1.90.0",
"sass-loader": "^16.0.5",
"stylelint": "^16.23.1",
"stylelint-config-recommended-vue": "^1.6.1",
"stylelint-config-standard-scss": "^15.0.1"
```

**Sass**: CSS preprocessor
- Variables, mixins, nesting
- SCSS syntax
- Modular styles

**Stylelint**: CSS/SCSS linter
- Style validation
- Best practices
- Custom rules

---

#### Git Hooks
```json
"husky": "^9.1.7",
"lint-staged": "^16.1.5"
```

**Husky**: Git hooks manager
- Pre-commit hooks
- Pre-push hooks
- Automated checks

**lint-staged**: Run commands on staged files
- Faster than linting entire project
- Only lint changed files
- Integrates with Husky

---

#### Babel
```json
"@babel/core": "^7.26.10",
"@babel/preset-env": "^7.26.10",
"@babel/plugin-transform-runtime": "^7.26.10"
```

**Babel**: JavaScript compiler
- Transpiles modern JS
- Polyfill injection
- Plugin system

---

#### Utilities
```json
"npm-run-all": "^4.1.5",
"chokidar-cli": "^3.0.0",
"onchange": "^7.1.0"
```

**npm-run-all**: Run multiple scripts
- Sequential or parallel
- Cross-platform

**chokidar-cli**: File watching
- Used for lint:watch
- Cross-platform watcher

**onchange**: Watch and run commands
- Used for prettier-watch
- Simple file watching

---

## Development Workflow

### Local Development Modes

#### 1. Standalone Mode (Fastest)
```bash
npm run serve:standalone
```

**Use when:**
- Developing components in isolation
- Fastest iteration with HMR
- Don't need single-spa integration

**Features:**
- Vite dev server with HMR
- Instant updates on save
- Uses `.env.standalone` config

---

#### 2. Full Development Mode
```bash
npm run serve
```

**Use when:**
- Testing production build
- Developing micro-frontend
- Need accurate production environment

**Features:**
- Production build with watch
- Linting on changes
- Mirrors deployment environment

---

#### 3. Preview Production Build
```bash
npm run build
npm run dev
```

**Use when:**
- Final testing before deployment
- Debugging production issues
- Performance testing

---

### Testing Workflow

#### Development Testing
```bash
npm run test:unit:watch
```

**Workflow:**
1. Make code changes
2. Tests automatically re-run
3. See instant feedback
4. Fix issues
5. Repeat

---

#### CI/CD Testing
```bash
npm run test:unit
```

**Purpose:**
- Full test suite execution
- Code coverage collection
- Exit code for CI/CD

---

### Code Quality Workflow

#### Before Commit
```bash
npm run lint
npm run prettier
```

**Automated via Husky:**
- Pre-commit hook runs lint-staged
- Lints and formats only staged files
- Prevents committing bad code

---

#### During Development
```bash
npm run serve  # Includes lint:init:watch
```

**Auto-linting:**
- Continuous feedback
- Catch issues immediately
- Maintain code quality

---

## Version Management

### Caret (^) - Minor and Patch Updates
```json
"vue": "^3.5.13"
```

**Allows versions**: `3.5.13` to `3.x.x`
- Locks major version
- Gets bug fixes and new features
- Safe for most packages

**Example:**
- ✅ `3.5.14` (patch)
- ✅ `3.6.0` (minor)
- ❌ `4.0.0` (major)

---

### Tilde (~) - Patch Updates Only
```json
"vue": "~3.5.13"
```

**Allows versions**: `3.5.13` to `3.5.x`
- Locks minor version
- Only bug fixes
- More conservative

**Example:**
- ✅ `3.5.14` (patch)
- ❌ `3.6.0` (minor)
- ❌ `4.0.0` (major)

---

### Exact Version - No Updates
```json
"typescript": "4.9"
```

**Allows versions**: Exactly `4.9.x`
- Maximum stability
- Predictable builds
- Used for critical tools

**When to use:**
- Critical dependencies (TypeScript)
- Known compatibility issues
- Reproducible builds

---

### package-lock.json

**Purpose:**
- Locks exact versions of all dependencies
- Includes transitive (nested) dependencies
- Ensures reproducible builds across machines
- **Always commit to version control**

**When regenerated:**
- `npm install` with new packages
- `npm update`
- Resolving dependency conflicts
- Version range resolution

---

## Troubleshooting

### Issue: npm install fails with 401 Unauthorized

**Cause:** Missing GitHub Package authentication

**Solution:**
1. Verify global `~/.npmrc` has GitHub token
2. Check token has `read:packages` permission
3. Try clearing cache:
```bash
npm cache clean --force
npm install
```

---

### Issue: Scripts don't run

**Symptoms:**
- `command not found` errors
- Script exits immediately

**Solutions:**

1. **Check script name:**
```bash
npm run <exact-script-name>
```

2. **Verify dependencies installed:**
```bash
npm install
```

3. **Check package.json syntax:**
- No trailing commas
- Proper JSON formatting
- Valid script commands

---

### Issue: Tests fail with module resolution errors

**Cause:** Jest not configured for TypeScript/Vue

**Solution:**
1. Check `jest.config.cjs` exists
2. Verify transformers installed:
```bash
npm list @vue/vue3-jest ts-jest babel-jest
```

3. Check `tsconfig.json` paths

---

### Issue: Linting errors appear only in CI

**Cause:** Different ESLint versions or configs

**Solutions:**

1. **Lock ESLint version:**
```json
"eslint": "9.30.1"  // Remove ^
```

2. **Verify config locally:**
```bash
npm run lint
```

3. **Check git hook execution:**
```bash
npm run prepare
```

---

### Issue: Build succeeds but app doesn't work

**Common causes:**

1. **Environment variables not set**
- Check `.env.local` exists
- Verify `VITE_*` prefix for client-side vars

2. **Missing dependencies**
```bash
npm install
```

3. **TypeScript errors ignored**
- Check console for TS errors
- Run: `tsc --noEmit`

---

### Issue: Hot Module Replacement (HMR) not working

**Solutions:**

1. **Use standalone mode:**
```bash
npm run serve:standalone
```

2. **Check Vite config:**
- Verify `server.hmr` not disabled

3. **Clear cache:**
```bash
rm -rf node_modules/.vite
npm run serve:standalone
```

---

### Issue: Peer dependency warnings

**Explanation:**
- Usually informational, not errors
- Package expects specific version of peer

**When to act:**
- If functionality breaks
- If warning mentions security issue

**Solution:**
```bash
npm install <peer-package>@<suggested-version>
```

---

## Common Commands Reference

### Development
```bash
npm run serve:standalone  # Standalone with HMR
npm run serve             # Full development workflow
npm run build:watch       # Continuous build
npm run dev               # Preview production build
```

### Building
```bash
npm run build             # Production build
```

### Testing
```bash
npm run test:unit         # Run tests once
npm run test:unit:watch   # Interactive testing
```

### Code Quality
```bash
npm run lint              # Check code
npm run prettier          # Format code
npm run lint:watch        # Auto-lint
npm run prettier-watch    # Auto-format
```

### Package Management
```bash
npm install               # Install all dependencies
npm install <package>     # Add dependency
npm install -D <package>  # Add dev dependency
npm update                # Update packages
npm outdated              # Check outdated packages
npm audit                 # Security audit
npm audit fix             # Fix vulnerabilities
```

---

## Best Practices

### 1. Always use package-lock.json
- Commit to version control
- Ensures consistent installs
- Faster CI/CD builds

### 2. Pin critical dependencies
```json
"typescript": "4.9"  // Exact version
```

### 3. Separate runtime from dev dependencies
- Keep production bundle small
- Faster production installs

### 4. Use scripts for common tasks
- Consistent across team
- Self-documenting
- Easy onboarding

### 5. Keep dependencies updated
```bash
npm outdated        # Check for updates
npm update          # Update safely
```

### 6. Regular security audits
```bash
npm audit           # Check vulnerabilities
npm audit fix       # Auto-fix issues
```

---

## Resources

- [npm Documentation](https://docs.npmjs.com/)
- [package.json Reference](https://docs.npmjs.com/cli/v10/configuring-npm/package-json)
- [Semantic Versioning](https://semver.org/)
- [npm Scripts](https://docs.npmjs.com/cli/v10/using-npm/scripts)
- [Vite Documentation](https://vite.dev/)
- [Vue 3 Documentation](https://vuejs.org/)
- [Jest Documentation](https://jestjs.io/)
- [ESLint Documentation](https://eslint.org/)
