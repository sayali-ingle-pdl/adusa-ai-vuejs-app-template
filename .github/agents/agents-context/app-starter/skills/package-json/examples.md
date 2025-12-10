# Package.json Examples

## Latest Versions Configuration

**When user requests "latest configuration" or "use latest versions":**

See `reference/latest-versions.md` for the complete template that uses dynamic version resolution.

**Key Features**:
- All versions resolved from `../../versions.json`
- Packages marked as `"latest"` are fetched from npm registry
- Automatic updates to newest compatible versions
- Fallback to pinned versions if network unavailable

**Example versions.json for latest configuration**:
```json
{
  "core": {
    "vue": "latest",
    "vite": "latest",
    "typescript": "latest"
  },
  "dependencies": {
    "vue-router": "latest",
    "vuex": "latest",
    "axios": "latest"
  },
  "devDependencies": {
    "eslint": "latest",
    "prettier": "latest"
  }
}
```

**Result**: All packages use the latest versions available on npm at generation time.

---

## Example: Complete Single-spa Micro Frontend Application

```json
{
  "name": "@pdl-fulfillment-omni/pdl-fulfillment-omni-inventory-manager-web",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite preview --port 8089",
    "build:watch": "vite build --watch",
    "serve": "npm-run-all --parallel build:watch dev lint:init:watch",
    "build": "vite build",
    "test:unit": "jest",
    "test:unit:watch": "jest --watch",
    "lint:init:watch": "npm run lint && npm run lint:watch",
    "lint:watch": "chokidar \"src/**/*.{js,ts,vue}\" -i node_modules -i dist -i .git --debounce 500 --initial false -c \"npm run lint\"",
    "lint": "eslint --ext .js,.ts,.vue src",
    "serve:standalone": "vite serve --port 8089 --mode standalone",
    "prettier": "prettier --write .",
    "prettier-watch": "onchange \"**/*\" \"!dist/**\" \"!node_modules/**\" -- prettier --write {{changed}}",
    "prepare": "husky"
  },
  "dependencies": {
    "@datadog/browser-rum": "^5.23.3",
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^1.0.3",
    "axios": "^1.4.0",
    "core-js": "^3.8.3",
    "date-fns": "^2.30.0",
    "date-fns-tz": "^2.0.0",
    "single-spa-vue": "^2.1.0",
    "vue": "^3.5.13",
    "vue-class-component": "^8.0.0-0",
    "vue-router": "^4.1.6",
    "vue-tippy": "^6.7.1",
    "vuex": "^4.0.0"
  },
  "devDependencies": {
    "@babel/core": "^7.26.10",
    "@babel/plugin-transform-runtime": "^7.26.10",
    "@babel/preset-env": "^7.26.10",
    "@eslint/eslintrc": "^3.3.1",
    "@eslint/js": "^9.30.1",
    "@types/jest": "^27.0.1",
    "@types/jsdom": "^21.1.1",
    "@types/node": "^20.17.30",
    "@typescript-eslint/eslint-plugin": "^8.35.1",
    "@typescript-eslint/parser": "^8.35.1",
    "@vitejs/plugin-vue": "^5.2.3",
    "@vue/eslint-config-typescript": "^14.5.1",
    "@vue/test-utils": "^2.0.0-0",
    "@vue/vue3-jest": "^27.0.0-alpha.1",
    "babel-jest": "^27.0.6",
    "chokidar-cli": "^3.0.0",
    "eslint": "^9.30.1",
    "eslint-config-prettier": "^10.1.8",
    "eslint-plugin-prettier": "^5.5.4",
    "eslint-plugin-vue": "^9.33.0",
    "husky": "^9.1.7",
    "jest": "^27.0.5",
    "lint-staged": "^16.1.5",
    "npm-run-all": "^4.1.5",
    "onchange": "^7.1.0",
    "prettier": "^3.6.2",
    "sass": "^1.90.0",
    "sass-loader": "^16.0.5",
    "stylelint": "^16.23.1",
    "stylelint-config-recommended-vue": "^1.6.1",
    "stylelint-config-standard-scss": "^15.0.1",
    "ts-jest": "^27.0.4",
    "typescript": "4.9",
    "vite": "^6.3.5",
    "vite-plugin-css-injected-by-js": "^3.5.2",
    "vite-svg-loader": "^5.1.0",
    "vue-eslint-parser": "^10.2.0"
  }
}
```

## What Each Section Does

### Package Metadata
```json
{
  "name": "@pdl-fulfillment-omni/pdl-fulfillment-omni-inventory-manager-web",
  "version": "0.1.0",
  "private": true,
  "type": "module"
}
```

**`name`:**
- Format: `@scope/package-name`
- Scope: Organization or team namespace (`@pdl-fulfillment-omni`)
- Package: Application name in kebab-case
- Used in: NPM registry, package imports

**`version`:**
- Semantic versioning: `MAJOR.MINOR.PATCH`
- `0.1.0`: Initial development version
- Increment based on changes (breaking.feature.fix)

**`private`:**
- `true`: Prevents accidental publish to NPM
- Internal applications should always be private

**`type`:**
- `"module"`: Use ES modules (import/export)
- Required for Vite and modern tooling
- Not CommonJS (require/module.exports)

### Scripts Explained

#### Development Scripts

**`dev`:**
```json
"dev": "vite preview --port 8089"
```
- Runs Vite preview server
- Serves production build locally
- Port 8089 (configurable)
- Tests production build before deployment

**`serve`:**
```json
"serve": "npm-run-all --parallel build:watch dev lint:init:watch"
```
- Runs multiple commands in parallel
- `build:watch`: Rebuilds on file changes
- `dev`: Serves built files
- `lint:init:watch`: Lints on changes
- Full development workflow

**`build:watch`:**
```json
"build:watch": "vite build --watch"
```
- Builds for production
- Watches files for changes
- Rebuilds automatically
- Used with preview server

**`serve:standalone`:**
```json
"serve:standalone": "vite serve --port 8089 --mode standalone"
```
- Development server without single-spa
- Uses `.env.standalone` configuration
- Tests app independently
- Port 8089

#### Build Scripts

**`build`:**
```json
"build": "vite build"
```
- Production build
- Minifies and optimizes code
- Outputs to `dist/` directory
- Creates SystemJS bundle for single-spa

#### Testing Scripts

**`test:unit`:**
```json
"test:unit": "jest"
```
- Runs all unit tests
- Uses jest.config.cjs configuration
- Collects coverage
- Exit after tests complete

**`test:unit:watch`:**
```json
"test:unit:watch": "jest --watch"
```
- Runs tests in watch mode
- Re-runs on file changes
- Interactive test runner
- Doesn't collect coverage by default

#### Linting Scripts

**`lint`:**
```json
"lint": "eslint --ext .js,.ts,.vue src"
```
- Lints JavaScript, TypeScript, and Vue files
- Only checks `src/` directory
- Uses eslint.config.cjs configuration
- Exits with error if issues found

**`lint:watch`:**
```json
"lint:watch": "chokidar \"src/**/*.{js,ts,vue}\" -i node_modules -i dist -i .git --debounce 500 --initial false -c \"npm run lint\""
```
- Watches source files for changes
- Runs lint on file change
- Debounce 500ms (waits for typing to finish)
- Ignores node_modules, dist, .git
- Uses chokidar for file watching

**`lint:init:watch`:**
```json
"lint:init:watch": "npm run lint && npm run lint:watch"
```
- Runs lint once immediately
- Then starts watch mode
- Ensures clean start

#### Formatting Scripts

**`prettier`:**
```json
"prettier": "prettier --write ."
```
- Formats all files in project
- Uses .prettierrc configuration
- Writes changes back to files
- One-time format

**`prettier-watch`:**
```json
"prettier-watch": "onchange \"**/*\" \"!dist/**\" \"!node_modules/**\" -- prettier --write {{changed}}"
```
- Watches all files for changes
- Formats changed files automatically
- Excludes dist and node_modules
- Uses onchange package

#### Git Hooks

**`prepare`:**
```json
"prepare": "husky"
```
- Runs automatically on `npm install`
- Initializes Husky git hooks
- Sets up pre-commit hooks
- Configures lint-staged

### Dependencies

#### Core Dependencies

**`vue: "^3.5.13"`**
- Vue 3 framework
- Composition API
- TypeScript support

**`vue-router: "^4.1.6"`**
- Official Vue router
- Client-side routing
- History mode support

**`vuex: "^4.0.0"`**
- State management
- Centralized store
- Vue 3 compatible

#### Single-spa Integration

**`single-spa-vue: "^2.1.0"`**
- Single-spa adapter for Vue
- Micro-frontend integration
- Lifecycle management

#### Utilities

**`axios: "^1.4.0"`**
- HTTP client
- Promise-based
- Request/response interceptors

**`core-js: "^3.8.3"`**
- JavaScript polyfills
- Browser compatibility
- Modern features in old browsers

**`date-fns: "^2.30.0"`**
- Date manipulation library
- Modern, lightweight
- Tree-shakeable

**`date-fns-tz: "^2.0.0"`**
- Timezone support for date-fns
- IANA timezone database
- Timezone conversions

**`vue-tippy: "^6.7.1"`**
- Tooltip component
- Based on Tippy.js
- Accessible tooltips

**`vue-class-component: "^8.0.0-0"`**
- Class-style component syntax
- TypeScript decorators
- Alternative to Composition API

#### Monitoring

**`@datadog/browser-rum: "^5.23.3"`**
- Datadog Real User Monitoring
- Performance tracking
- Error monitoring
- User session recording

#### Component Library

**`@royalaholddelhaize/pdl-spectrum-component-library-web`**
- PDL Spectrum component library
- Shared UI components
- Consistent design system
- Private package from GitHub registry

### Dev Dependencies

#### Build Tools

**`vite: "^6.3.5"`**
- Build tool and dev server
- Lightning-fast HMR
- Optimized production builds

**`@vitejs/plugin-vue: "^5.2.3"`**
- Vite plugin for Vue SFC support
- Compiles .vue files
- Hot module replacement

**`vite-plugin-css-injected-by-js: "^3.5.2"`**
- Injects CSS via JavaScript
- Required for single-spa CSS isolation
- Prevents style conflicts

**`vite-svg-loader: "^5.1.0"`**
- Import SVG as Vue components
- Inline SVG support
- Optimized SVG output

#### TypeScript

**`typescript: "4.9"`**
- TypeScript compiler
- Type checking
- Exact version for consistency

**`@types/node: "^20.17.30"`**
- Node.js type definitions
- For build scripts and tooling

#### Testing

**`jest: "^27.0.5"`**
- Testing framework
- Snapshot testing
- Code coverage

**`ts-jest: "^27.0.4"`**
- TypeScript preprocessor for Jest
- Compiles TS for tests
- Type checking in tests

**`@vue/vue3-jest: "^27.0.0-alpha.1"`**
- Jest transformer for Vue 3 SFC
- Compiles .vue files for tests
- Template/script/style processing

**`babel-jest: "^27.0.6"`**
- Babel transformer for Jest
- Handles modern JavaScript
- Uses babel.config.cjs

**`@vue/test-utils: "^2.0.0-0"`**
- Official Vue testing utilities
- Component mounting
- Wrapper API for assertions

**`@types/jest: "^27.0.1"`**
- Jest type definitions
- TypeScript support in tests

**`@types/jsdom: "^21.1.1"`**
- jsdom type definitions
- Browser environment types

#### Linting

**`eslint: "^9.30.1"`**
- JavaScript/TypeScript linter
- Flat config format (ESLint 9)
- Code quality checks

**`eslint-plugin-vue: "^9.33.0"`**
- Vue-specific linting rules
- Template syntax checking
- Vue best practices

**`@typescript-eslint/eslint-plugin: "^8.35.1"`**
- TypeScript linting rules
- Type-aware linting

**`@typescript-eslint/parser: "^8.35.1"`**
- TypeScript parser for ESLint
- Understands TS syntax

**`vue-eslint-parser: "^10.2.0"`**
- Vue file parser for ESLint
- Parses .vue files

**`@vue/eslint-config-typescript: "^14.5.1"`**
- Shared ESLint config for Vue + TypeScript
- Pre-configured rules

**`eslint-config-prettier: "^10.1.8"`**
- Disables ESLint rules that conflict with Prettier
- Ensures compatibility

**`eslint-plugin-prettier: "^5.5.4"`**
- Runs Prettier as ESLint rule
- Format errors shown as lint errors

**`@eslint/js: "^9.30.1"`**
- ESLint JavaScript rules
- Core rule definitions

**`@eslint/eslintrc: "^3.3.1"`**
- ESLint configuration utilities
- Legacy config compatibility

#### Formatting

**`prettier: "^3.6.2"`**
- Code formatter
- Opinionated styling
- Consistent code style

#### Styling

**`sass: "^1.90.0"`**
- SCSS/Sass compiler
- CSS preprocessor
- Variables, mixins, nesting

**`sass-loader: "^16.0.5"`**
- Webpack/Vite loader for Sass
- Compiles .scss files

**`stylelint: "^16.23.1"`**
- CSS/SCSS linter
- Style validation
- Best practices enforcement

**`stylelint-config-recommended-vue: "^1.6.1"`**
- Stylelint config for Vue
- Vue-specific style rules

**`stylelint-config-standard-scss: "^15.0.1"`**
- Standard SCSS rules
- Community best practices

#### Git Hooks

**`husky: "^9.1.7"`**
- Git hooks manager
- Pre-commit hooks
- Automated quality checks

**`lint-staged: "^16.1.5"`**
- Run commands on staged files
- Faster than linting entire project
- Integrates with Husky

#### Babel

**`@babel/core: "^7.26.10"`**
- Babel compiler core
- JavaScript transformation

**`@babel/preset-env: "^7.26.10"`**
- Smart preset for target environments
- Automatic polyfills
- Browser compatibility

**`@babel/plugin-transform-runtime: "^7.26.10"`**
- Babel runtime helpers
- Reduces bundle size
- Avoids helper duplication

#### Utilities

**`npm-run-all: "^4.1.5"`**
- Run multiple npm scripts
- Sequential or parallel execution
- Cross-platform compatible

**`chokidar-cli: "^3.0.0"`**
- File watching CLI
- Cross-platform file watcher
- Used for lint:watch

**`onchange: "^7.1.0"`**
- Watch files and run commands
- Used for prettier-watch
- Simple file watching

## Scripts Deep Dive

### Development Workflow Scripts

#### `serve` - Full Development Mode
```json
"serve": "npm-run-all --parallel build:watch dev lint:init:watch"
```

**What happens:**
1. **build:watch**: Vite builds in watch mode → `dist/`
2. **dev**: Vite preview serves from `dist/` on port 8089
3. **lint:init:watch**: Lints initially, then on changes

**Use case:**
- Testing production build locally
- Micro-frontend development
- Mirrors production environment

**Run:**
```bash
npm run serve
```

#### `dev` - Preview Production Build
```json
"dev": "vite preview --port 8089"
```

**What it does:**
- Serves files from `dist/` directory
- Not a development server (no HMR)
- Tests production build
- Port 8089

**Use case:**
- Verify production build works
- Test before deployment

#### `serve:standalone` - Standalone Development
```json
"serve:standalone": "vite serve --port 8089 --mode standalone"
```

**What it does:**
- Vite dev server (with HMR)
- Uses `.env.standalone` file
- STANDALONE_SINGLE_SPA=true
- Independent of launcher app

**Use case:**
- Develop without single-spa launcher
- Faster iteration
- Component development

**Run:**
```bash
npm run serve:standalone
```

### Build Scripts

#### `build` - Production Build
```json
"build": "vite build"
```

**What it does:**
- Compiles TypeScript
- Bundles JavaScript
- Processes Vue components
- Optimizes and minifies
- Outputs to `dist/`

**Output:**
```
dist/
├── index.html
├── assets/
│   ├── app.abc123.js
│   └── index.xyz789.css
└── ...
```

#### `build:watch` - Watch Mode Build
```json
"build:watch": "vite build --watch"
```

**What it does:**
- Initial production build
- Watches for file changes
- Rebuilds on change
- Continuous build

**Use case:**
- Used with `serve` script
- Developing micro-frontend
- Testing build integration

### Testing Scripts

#### `test:unit` - Run Tests
```json
"test:unit": "jest"
```

**What it does:**
- Runs all `.spec.ts` files
- Collects coverage
- Outputs results
- Exits with code

**Run:**
```bash
npm run test:unit
```

#### `test:unit:watch` - Watch Mode Tests
```json
"test:unit:watch": "jest --watch"
```

**What it does:**
- Interactive test runner
- Re-runs on file changes
- Shows only relevant tests
- Faster iteration

**Run:**
```bash
npm run test:unit:watch
```

### Linting Scripts

#### `lint` - Lint Code
```json
"lint": "eslint --ext .js,.ts,.vue src"
```

**What it does:**
- Lints all JS/TS/Vue files in src/
- Checks code quality
- Reports errors and warnings
- Exits with error if issues found

**Run:**
```bash
npm run lint
```

#### `lint:watch` - Watch and Lint
```json
"lint:watch": "chokidar \"src/**/*.{js,ts,vue}\" -i node_modules -i dist -i .git --debounce 500 --initial false -c \"npm run lint\""
```

**Parameters:**
- **Watch pattern**: `src/**/*.{js,ts,vue}`
- **Ignore**: node_modules, dist, .git
- **Debounce**: 500ms (wait for typing to stop)
- **Initial**: false (don't run on start)
- **Command**: `npm run lint`

**Use case:**
- Get instant feedback while coding
- Auto-lint on save

#### `lint:init:watch` - Initial Lint + Watch
```json
"lint:init:watch": "npm run lint && npm run lint:watch"
```

**What it does:**
1. Runs lint once (ensures clean start)
2. Starts watch mode
3. Continues linting on changes

### Formatting Scripts

#### `prettier` - Format All Files
```json
"prettier": "prettier --write ."
```

**What it does:**
- Formats all files in project
- Overwrites files with formatted version
- Uses .prettierrc config
- Respects .prettierignore

**Run:**
```bash
npm run prettier
```

#### `prettier-watch` - Auto-format on Change
```json
"prettier-watch": "onchange \"**/*\" \"!dist/**\" \"!node_modules/**\" -- prettier --write {{changed}}"
```

**What it does:**
- Watches all files
- Excludes dist and node_modules
- Formats changed files only
- Uses onchange package

**Use case:**
- Auto-format on save (alternative to editor plugin)
- Ensure consistency

### Git Hooks

#### `prepare` - Husky Setup
```json
"prepare": "husky"
```

**What it does:**
- Runs automatically after `npm install`
- Initializes Husky
- Installs git hooks
- Sets up pre-commit hooks

**Triggers:**
- `npm install`
- `npm ci`
- After cloning repository

## Dependencies vs DevDependencies

### Runtime Dependencies (dependencies)
**Included in production build:**
- Vue framework
- Vue Router
- Vuex
- Axios
- Datadog RUM
- Component libraries
- Date utilities

**Why runtime:**
- App won't work without them
- Code imported in source
- Bundled in output

### Development Dependencies (devDependencies)
**NOT included in production build:**
- Vite
- TypeScript
- Jest
- ESLint
- Prettier
- Babel
- Type definitions

**Why dev only:**
- Build tools
- Testing tools
- Linting/formatting
- Only needed during development

## Installation

### Install All Dependencies
```bash
npm install
```

### Install Production Only
```bash
npm install --production
```

### Install Specific Package
```bash
# Dependency
npm install vue-router

# Dev dependency
npm install --save-dev jest
```

### Update Dependencies
```bash
# Check outdated
npm outdated

# Update all
npm update

# Update specific
npm update vite
```

## Version Pinning

### Caret (^) - Minor Updates
```json
"vue": "^3.5.13"
```
- Allows: `3.5.13` to `3.x.x`
- Locks major version
- Gets bug fixes and features
- Safe for most packages

### Tilde (~) - Patch Updates
```json
"vue": "~3.5.13"
```
- Allows: `3.5.13` to `3.5.x`
- Locks minor version
- Only bug fixes
- More conservative

### Exact Version - No Updates
```json
"typescript": "4.9"
```
- Exact version only
- No automatic updates
- Maximum stability
- Used for critical tools

## package-lock.json

**Purpose:**
- Locks exact versions of all dependencies
- Includes transitive dependencies
- Ensures reproducible builds
- Commit to version control

**When regenerated:**
- `npm install` with new/updated packages
- `npm update`
- Resolving dependency conflicts

## Common Commands

### Development
```bash
npm run serve:standalone  # Standalone development
npm run serve             # Full development workflow
npm run build:watch       # Build in watch mode
```

### Build
```bash
npm run build             # Production build
```

### Testing
```bash
npm run test:unit         # Run tests once
npm run test:unit:watch   # Interactive testing
```

### Quality
```bash
npm run lint              # Check code quality
npm run prettier          # Format code
npm run lint:watch        # Auto-lint
npm run prettier-watch    # Auto-format
```

## Customization Examples

### Change Port
```json
{
  "scripts": {
    "dev": "vite preview --port 3000",
    "serve:standalone": "vite serve --port 3000 --mode standalone"
  }
}
```

### Add E2E Tests
```json
{
  "scripts": {
    "test:e2e": "cypress open",
    "test:e2e:ci": "cypress run"
  },
  "devDependencies": {
    "cypress": "^13.0.0"
  }
}
```

### Add Build Modes
```json
{
  "scripts": {
    "build": "vite build",
    "build:dev": "vite build --mode development",
    "build:staging": "vite build --mode staging",
    "build:prod": "vite build --mode production"
  }
}
```

### Add Type Checking
```json
{
  "scripts": {
    "type-check": "vue-tsc --noEmit",
    "build": "npm run type-check && vite build"
  },
  "devDependencies": {
    "vue-tsc": "^2.0.0"
  }
}
```

## Example 2: Standalone Application (No single-spa)

```json
{
  "name": "@mycompany/my-app",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite --port 3000",
    "build": "vite build",
    "preview": "vite preview",
    "test:unit": "jest",
    "test:unit:watch": "jest --watch",
    "lint": "eslint --ext .js,.ts,.vue src",
    "prettier": "prettier --write .",
    "prepare": "husky"
  },
  "dependencies": {
    "axios": "^1.4.0",
    "core-js": "^3.8.3",
    "vue": "^3.5.13",
    "vue-router": "^4.1.6",
    "vuex": "^4.0.0"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^5.2.3",
    "@vue/test-utils": "^2.0.0-0",
    "eslint": "^9.30.1",
    "eslint-plugin-vue": "^9.33.0",
    "husky": "^9.1.7",
    "jest": "^27.0.5",
    "prettier": "^3.6.2",
    "sass": "^1.90.0",
    "typescript": "4.9",
    "vite": "^6.3.5"
  }
}
```

## Example 3: Minimal Setup (No testing, no linting)

```json
{
  "name": "@mycompany/simple-app",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite --port 3000",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "vue": "^3.5.13",
    "vue-router": "^4.1.6"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^5.2.3",
    "typescript": "4.9",
    "vite": "^6.3.5"
  }
}
```

## Key Differences

### Single-spa vs Standalone
- **Single-spa**: Includes `single-spa-vue` dependency and build/serve scripts for micro frontend integration
- **Standalone**: Standard Vite dev server with simpler scripts

### Full vs Minimal
- **Full**: Complete testing, linting, formatting, and git hooks setup
- **Minimal**: Only essential build tools, suitable for prototypes or learning

### Port Configuration
- Development server port is configurable via `{{default_port}}` parameter
- Common ports: 3000, 8080, 8089, 5173 (Vite default)

## Troubleshooting

### Issue: npm install fails
**Solution:**
1. Check .npmrc is configured for private packages
2. Verify GH_PACKAGE_LIBRARY_TOKEN is set
3. Clear npm cache: `npm cache clean --force`

### Issue: Scripts don't run
**Solution:**
1. Check script name: `npm run <script-name>`
2. Verify dependencies are installed
3. Check for syntax errors in package.json

### Issue: Peer dependency warnings
**Solution:**
Most warnings are informational; install suggested peer dependencies if needed.

## Resources

- [npm Documentation](https://docs.npmjs.com/)
- [package.json Reference](https://docs.npmjs.com/cli/v10/configuring-npm/package-json)
- [Semantic Versioning](https://semver.org/)
- [npm Scripts](https://docs.npmjs.com/cli/v10/using-npm/scripts)

