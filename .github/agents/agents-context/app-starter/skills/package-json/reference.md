# Package.json Reference

**IMPORTANT FOR COPILOT**: Always use the latest stable versions of all packages. The versions shown here are examples only—DO NOT copy them directly.

To determine the latest stable version of a package, use one of the following methods:

- Run `npm view <package> version` to get the latest published stable version.
- Or, check the package's page on [npmjs.com](https://www.npmjs.com/package/<package>).

When specifying versions in `package.json`, use the caret (`^`) prefix (e.g., `^1.2.3`) to allow updates to the latest minor and patch versions, unless a different range is required for compatibility.
## Required NPM Scripts

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

## Package Selection Rules

1. **Check deprecation status** before adding any package:
   - Run `npm view <package>` and check for a `deprecated` warning in the output.
   - If a package is deprecated, search for a maintained alternative and prefer that.
   - If you must use a deprecated package, document the reason and the alternatives considered in the project README or a `DECISIONS.md` file.
2. **Use latest stable versions** for all packages
3. **Ensure Jest ecosystem versions match** - babel-jest, ts-jest, and @types/jest should match the Jest major version
4. **Use caret (^) for most packages** - allows minor/patch updates
5. **Remove optional packages** if not needed for the project