# Package.json Reference

## Complete NPM Scripts

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

## Dependencies

### Core Dependencies
```json
{
  "vue": "{{vue_version}}",
  "vue-router": "^4.1.6",
  "vuex": "^4.0.0",
  "axios": "^1.4.0",
  "core-js": "^3.8.3"
}
```

### Single-spa Integration
```json
{
  "single-spa-vue": "^2.1.0"
}
```

### UI and Utilities
```json
{
  "@royalaholddelhaize/pdl-spectrum-component-library-web": "npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^1.0.3",
  "vue-class-component": "^8.0.0-0",
  "vue-tippy": "^6.7.1",
  "date-fns": "^2.30.0",
  "date-fns-tz": "^2.0.0"
}
```

### Monitoring
```json
{
  "@datadog/browser-rum": "^5.23.3"
}
```

## Dev Dependencies

### Build Tools
```json
{
  "vite": "{{vite_version}}",
  "@vitejs/plugin-vue": "^5.2.3",
  "vite-plugin-css-injected-by-js": "^3.5.2",
  "vite-svg-loader": "^5.1.0"
}
```

### TypeScript
```json
{
  "typescript": "{{typescript_version}}",
  "@types/node": "^20.17.30",
  "@types/jest": "^27.0.1",
  "@types/jsdom": "^21.1.1"
}
```

### Testing
```json
{
  "jest": "^27.0.5",
  "@vue/test-utils": "^2.0.0-0",
  "@vue/vue3-jest": "^27.0.0-alpha.1",
  "babel-jest": "^27.0.6",
  "ts-jest": "^27.0.4"
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
  "prettier": "^3.6.2",
  "stylelint": "^16.23.1",
  "stylelint-config-recommended-vue": "^1.6.1",
  "stylelint-config-standard-scss": "^15.0.1"
}
```

### SCSS
```json
{
  "sass": "^1.90.0",
  "sass-loader": "^16.0.5"
}
```

### Git Hooks and Utilities
```json
{
  "husky": "^9.1.7",
  "lint-staged": "^16.1.5",
  "npm-run-all": "^4.1.5",
  "chokidar-cli": "^3.0.0",
  "onchange": "^7.1.0"
}
```

## Notes

- **Single-spa integration**: Required for micro frontend architecture
- **Datadog**: Optional, remove if not using monitoring
- **Component Library**: Replace `@royalaholddelhaize/pdl-spectrum-component-library-web` with your own component library or remove
- **Version placeholders**: `{{vue_version}}`, `{{vite_version}}`, `{{typescript_version}}` must be replaced with actual values
