# Package.json Examples

Quick reference for generating package.json files for Vue 3 + Vite applications.

## Single-spa Micro Frontend (Full Setup)

```json
{
  "name": "@{{project_scope}}/{{application_name}}",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
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

## Standalone Application (No single-spa)

```json
{
  "name": "@{{project_scope}}/{{application_name}}",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite --port {{default_port}}",
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

## Minimal Setup (Prototypes)

```json
{
  "name": "@{{project_scope}}/{{application_name}}",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite --port {{default_port}}",
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

## Key Configuration Notes

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

## Important Notes

All packages should use latest stable or most compatible versions. Check reference.md for current version specifications. If packages are deprecated, suggest alternatives.

