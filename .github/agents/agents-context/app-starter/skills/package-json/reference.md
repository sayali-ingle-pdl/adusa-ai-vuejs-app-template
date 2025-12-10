# Package.json Reference

## ⚠️ CRITICAL INSTRUCTIONS FOR VERSION SELECTION ⚠️

**YOU MUST RUN `npm view <package> version` FOR EVERY SINGLE PACKAGE LISTED BELOW.**

### Version Fetching Rules:

1. **NEVER hardcode versions** - Always fetch the current latest version
2. **NEVER copy versions from this file** - These are examples only and WILL be outdated
3. **NEVER assume you know the version** - Even if you checked yesterday, check again
4. **ALWAYS run npm view** - This is the ONLY source of truth for current versions
5. **CHECK COMPATIBILITY** - Some packages must have matching versions (see compatibility notes below)

### How to Fetch Versions Efficiently:

Run all `npm view` commands in a single batch:
```bash
npm view vue version && npm view vue-router version && npm view vuex version
```

### Version Constraints & Compatibility:

**Jest Ecosystem Constraint (CRITICAL):**
- Check `@vue/vue3-jest` version FIRST
- If it's at v29.x, then ALL Jest packages must use v29:
  - `jest@^29.x.x`
  - `babel-jest@^29.x.x`
  - `ts-jest@^29.x.x`
  - `@types/jest@^29.x.x`
  - `jest-environment-jsdom@^29.x.x`
- **Do NOT use Jest 30 if @vue/vue3-jest doesn't support it**

**TypeScript Ecosystem:**
- `@typescript-eslint/*` packages should have compatible versions with `typescript`
- Check package peer dependencies if unsure

### After Package.json Creation:

**MANDATORY VERIFICATION:**
```bash
npm install
npm outdated  # Should return NO outdated packages
```

If `npm outdated` shows outdated packages, you did something wrong. Fix it before proceeding.

## Required NPM Scripts

**Important Note**
These are the desired scripts. If incase any of the script is outdated, replace it with the new script structure.

**For Single-spa Micro Frontend:**
```json
{
  "dev": "vite preview --port {{default_port}}",
  "build:watch": "vite build --watch",
  "serve": "npm-run-all --parallel build:watch dev lint:init:watch",
  "build": "vite build",
  "test:unit": "jest",
  "test:unit:watch": "jest --watch",
  "lint:init:watch": "npm run lint && npm run lint:watch",
  "lint:watch": "chokidar \"src/**/*.{js,ts,vue}\" -i node_modules -i dist -i .git --debounce 500 --initial false -c \"npm run lint\"",
  "lint": "eslint --ext .js,.ts,.vue src",
  "serve:standalone": "vite serve --port {{default_port}} --mode standalone",
  "prettier": "prettier --write .",
  "prettier-watch": "onchange \"**/*\" \"!dist/**\" \"!node_modules/**\" -- prettier --write {{changed}}",
  "prepare": "husky"
}
```

**For Standalone App (no single-spa):**
```json
{
  "dev": "vite --port {{default_port}}",
  "build": "vite build",
  "preview": "vite preview",
  "test:unit": "jest",
  "test:unit:watch": "jest --watch",
  "lint": "eslint --ext .js,.ts,.vue src",
  "prettier": "prettier --write .",
  "prepare": "husky"
}
```

## Required Dependencies

### Always Include (Runtime)
- `vue` - Latest stable (Vue 3)
- `vue-router` - Latest stable (compatible with Vue 3)
- `vuex` - Latest stable (compatible with Vue 3)
- `axios` - Latest stable
- `core-js` - Latest stable

### Include for Single-spa Only
- `single-spa-vue` - Latest stable

### Optional (Project-specific)
- `@datadog/browser-rum` - If using Datadog monitoring
- `@royalaholddelhaize/pdl-spectrum-component-library-web` - Internal component library (project-specific)
- `vue-class-component` - If using class-style components
- `vue-tippy` - If using tooltips
- `date-fns` - Date manipulation
- `date-fns-tz` - Timezone support for date-fns

## Required Dev Dependencies

### Build Tools (Always)
- `vite` - Latest stable
- `@vitejs/plugin-vue` - Latest stable
- `typescript` - Latest stable
- `@types/node` - Latest stable

### Single-spa Build Tools (If using single-spa)
- `vite-plugin-css-injected-by-js` - Latest stable
- `vite-svg-loader` - Latest stable

### Testing (Always)
- `jest` - Latest stable
- `@vue/test-utils` - Latest stable (Vue 3)
- `@vue/vue3-jest` - Latest stable (must match Jest major version)
- `babel-jest` - Latest stable (must match Jest major version)
- `ts-jest` - Latest stable (must match Jest major version)
- `@types/jest` - Latest stable (must match Jest major version)
- `@types/jsdom` - Latest stable

### Babel (Always)
- `@babel/core` - Latest stable
- `@babel/preset-env` - Latest stable
- `@babel/plugin-transform-runtime` - Latest stable

### Linting (Always)
- `eslint` - Latest stable (flat config format)
- `@eslint/js` - Latest stable
- `@eslint/eslintrc` - Latest stable
- `eslint-plugin-vue` - Latest stable
- `eslint-plugin-prettier` - Latest stable
- `eslint-config-prettier` - Latest stable
- `@typescript-eslint/eslint-plugin` - Latest stable
- `@typescript-eslint/parser` - Latest stable
- `@vue/eslint-config-typescript` - Latest stable
- `vue-eslint-parser` - Latest stable

### Formatting (Always)
- `prettier` - Latest stable
- `stylelint` - Latest stable
- `stylelint-config-recommended-vue` - Latest stable
- `stylelint-config-standard-scss` - Latest stable

### SCSS (Always)
- `sass` - Latest stable
- `sass-loader` - Latest stable

### Git Hooks & Utilities (Always)
- `husky` - Latest stable
- `lint-staged` - Latest stable
- `npm-run-all` - Latest stable
- `chokidar-cli` - Latest stable
- `onchange` - Latest stable

## Notes

### Metadata
- `name`: Format `@{scope}/{application_name}` in kebab-case
- `private: true`: Prevents NPM publication
- `type: "module"`: ES modules for Vite compatibility

### Script Patterns
- **Single-spa**: Uses `vite preview` + `build:watch` for micro-frontend development
- **Standalone**: Uses standard `vite` dev server
- **Full setup**: Includes parallel linting, watching, and formatting scripts

### Dependency Categories
- **Runtime** (dependencies): Vue, Router, Vuex, Axios, utilities
- **Build** (devDependencies): Vite, TypeScript, Babel, plugins
- **Testing** (devDependencies): Jest, Vue Test Utils, transformers
- **Quality** (devDependencies): ESLint, Prettier, Stylelint, Husky

### Version Patterns
- `^x.y.z`: Allows minor/patch updates (most packages)
- Exact version: Critical tools like TypeScript (4.9)
- Check reference.md for specific version requirements

### Template Variables
- `{{project_scope}}`: NPM organization (e.g., `@pdl-fulfillment-omni`)
- `{{application_name}}`: App name in kebab-case (e.g., `omni-inventory-manager-web`)
- `{{default_port}}`: Dev server port (e.g., `8089`)

### Build Format Differences
- **Single-spa**: Requires `single-spa-vue`, `vite-plugin-css-injected-by-js`, SystemJS format
- **Standalone**: Standard ES module build, no single-spa dependencies

## Package Selection Rules

1. **Check deprecation status** before adding any package:
   - Run `npm view <package>` and check for a `deprecated` warning in the output.
   - If a package is deprecated, search for a maintained alternative and prefer that.
   - If you must use a deprecated package, document the reason and the alternatives considered in the project README or a `DECISIONS.md` file.
2. **Use latest stable versions** for all packages
3. **Ensure Jest ecosystem versions match** - babel-jest, ts-jest, and @types/jest should match the Jest major version
4. **Use caret (^) for most packages** - allows minor/patch updates
5. **Remove optional packages** if not needed for the project

## Troubleshooting: What to Do When npm outdated Shows Issues

### Scenario 1: Outdated packages found after installation

**Problem:** You ran `npm install` and then `npm outdated` shows packages are outdated.

**Root Cause:** You didn't fetch the latest versions before creating package.json.

**Solution:**
1. For each outdated package, run: `npm install <package>@latest`
2. Check if there are compatibility issues
3. Run `npm outdated` again to verify
4. Update the package.json generation process to prevent this in the future

### Scenario 2: Jest-related packages stuck at v29

**Problem:** `npm outdated` shows Jest 30 is available, but you're using Jest 29.

**Root Cause:** `@vue/vue3-jest` only supports Jest 29, so you're intentionally locked to v29.

**Solution:**
- This is EXPECTED and CORRECT
- Do NOT upgrade Jest packages to v30
- All Jest-related packages should stay at v29.x.x
- Consider recommending Vitest instead of Jest for future projects

### Scenario 3: @types/* packages are outdated

**Problem:** Type definition packages like `@types/node`, `@types/jsdom` are outdated.

**Root Cause:** These packages are independent and you forgot to check their versions.

**Solution:**
1. Run: `npm install @types/node@latest @types/jsdom@latest`
2. These packages have no compatibility constraints with Jest
3. Always fetch latest versions for @types/* packages

### Scenario 4: ESLint/Vue packages are outdated

**Problem:** `eslint-plugin-vue`, `vue-eslint-parser` are outdated.

**Root Cause:** You didn't check their latest versions.

**Solution:**
1. Check compatibility: `npm view eslint-plugin-vue peerDependencies`
2. If compatible with your ESLint version: `npm install eslint-plugin-vue@latest vue-eslint-parser@latest`
3. Test linting still works: `npm run lint`
