# Package.json Reference - Jest + Vuex Configuration

This reference is used when the user selects:
- **Testing Framework**: Jest
- **State Management**: Vuex

## Complete NPM Scripts

```json
{
  "dev": "vite preview --port {{default_port}}",
  "build:watch": "vite build --watch",
  "serve": "npm-run-all --parallel build:watch dev lint:init:watch",
  "build": "vite build",
  "test:unit": "jest",
  "test:unit:watch": "jest --watch",
  "test:coverage": "jest --coverage",
  "lint:init:watch": "npm run lint && npm run lint:watch",
  "lint:watch": "chokidar \"src/**/*.{js,ts,vue}\" -i node_modules -i dist -i .git --debounce 500 --initial false -c \"npm run lint\"",
  "lint": "eslint --ext .js,.ts,.vue src",
  "serve:standalone": "vite serve --port {{default_port}} --mode standalone",
  "prettier": "prettier --write .",
  "prettier-watch": "onchange \"**/*\" \"!dist/**\" \"!node_modules/**\" -- prettier --write {{changed}}",
  "prepare": "husky"
}
```

## Dependencies

### Core Dependencies
```json
{
  "vue": "{{vue_version}}",
  "vue-router": "^4.6.3",
  "vuex": "^4.1.0",
  "axios": "^1.13.2",
  "core-js": "^3.47.0"
}
```

### Single-spa Integration
```json
{
  "single-spa-vue": "^3.0.1"
}
```

### UI and Utilities
```json
{
  "@royalaholddelhaize/pdl-spectrum-component-library-web": "npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^1.0.3",
  "vue-class-component": "^8.0.0-0",
  "vue-tippy": "^6.7.1",
  "date-fns": "^4.1.0",
  "date-fns-tz": "^3.2.0"
}
```

**Note**: Component library is only included if BOTH `component_library` AND `github_token` are provided in config.

### Monitoring
```json
{
  "@datadog/browser-rum": "^6.24.1"
}
```

## Dev Dependencies

### Build Tools
```json
{
  "vite": "{{vite_version}}",
  "@vitejs/plugin-vue": "^6.0.2",
  "vite-plugin-css-injected-by-js": "^3.5.2",
  "vite-svg-loader": "^5.1.0"
}
```

### TypeScript
```json
{
  "typescript": "{{typescript_version}}",
  "@types/node": "^20.17.30",
  "@types/jest": "^30.0.0",
  "@types/jsdom": "^21.1.1"
}
```

### Testing (Jest)
```json
{
  "jest": "^30.2.0",
  "@vue/test-utils": "^2.4.6",
  "@vue/vue3-jest": "^29.2.6",
  "babel-jest": "^30.2.0",
  "ts-jest": "^29.4.6",
  "jest-environment-jsdom": "^30.2.0"
}
```

### Babel
```json
{
  "@babel/core": "^7.26.10",
  "@babel/plugin-transform-runtime": "^7.26.10",
  "@babel/preset-env": "^7.26.10"
}
```

### Linting and Formatting
```json
{
  "eslint": "^9.30.1",
  "@eslint/js": "^9.30.1",
  "@eslint/eslintrc": "^3.3.1",
  "eslint-plugin-vue": "^9.33.0",
  "eslint-plugin-prettier": "^5.5.4",
  "eslint-config-prettier": "^10.1.8",
  "@typescript-eslint/eslint-plugin": "^8.35.1",
  "@typescript-eslint/parser": "^8.35.1",
  "@vue/eslint-config-typescript": "^14.5.1",
  "vue-eslint-parser": "^10.2.0",
  "prettier": "^3.7.4",
  "stylelint": "^16.26.1",
  "stylelint-config-recommended-vue": "^1.6.1",
  "stylelint-config-standard-scss": "^15.0.1"
}
```

### SCSS
```json
{
  "sass": "^1.94.2",
  "sass-loader": "^16.0.6"
}
```

### Git Hooks and Utilities
```json
{
  "husky": "^9.1.7",
  "lint-staged": "^16.2.7",
  "npm-run-all": "^4.1.5",
  "chokidar-cli": "^3.0.0",
  "onchange": "^7.1.0"
}
```

## Notes

- **State Management**: Uses Vuex 4 with TypeScript support
- **Testing Framework**: Uses Jest 30.x with Vue Test Utils
- **Test Configuration**: Requires `jest.config.cjs`
- **Store Structure**: `src/store/` with modules in `src/store/modules/`
- **Single-spa integration**: Required for micro frontend architecture
- **Datadog**: Optional, remove if not using monitoring
- **Component Library**: Replace `@royalaholddelhaize/pdl-spectrum-component-library-web` with your own component library or remove
- **Version placeholders**: `{{vue_version}}`, `{{vite_version}}`, `{{typescript_version}}` must be replaced with actual values
