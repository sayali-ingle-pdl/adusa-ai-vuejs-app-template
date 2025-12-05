# Micro-Frontend Implementation Guide

## Overview

This document provides comprehensive guidance for generating micro-frontend applications using the single-spa framework with Vite and Vue 3.

## Validated Against

**Real Project**: `pdl-fulfillment-omni-inventory-manager-web`  
**Type**: Vite-based single-spa micro-frontend  
**Status**: ✅ Production application, all patterns validated

## Key Characteristics

A micro-frontend application in this template has the following characteristics:

1. **Uses single-spa** - Integrates with single-spa root config for micro frontend architecture
2. **SystemJS build format** - Outputs `system` format modules for dynamic loading
3. **Lifecycle exports** - Exports bootstrap, mount, and unmount functions
4. **Style isolation** - Manages CSS scoping through style tag caching
5. **Dual mode operation** - Can run standalone for development or within single-spa
6. **Launcher integration** - Connects to launcher app via `VITE_LAUNCHER_APP_URL`

## Required Files

### 1. Environment Files

#### `.env.local`
```bash
NODE_ENV=development
VITE_ACCESS_MANAGEMENT_BASE_URL=omni-access-manager/api
VITE_LAUNCHER_APP_URL=http://localhost:9000
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=your-app-id
VITE_DATADOG_CLIENT_TOKEN=your-client-token
```

**Purpose**: Local development environment variables  
**Key Variable**: `VITE_LAUNCHER_APP_URL` - URL of the single-spa root config

#### `.env.standalone`
```bash
STANDALONE_SINGLE_SPA=true
```

**Purpose**: Enables standalone mode for development without launcher  
**Usage**: Used with `npm run serve:standalone` or `vite serve --mode standalone`

### 2. Main Entry Point (`src/main.ts`)

```typescript
import { h, createApp } from 'vue';
import singleSpaVue, { SingleSpaVueLifecycles } from 'single-spa-vue';
import App from './App.vue';
import router from './router';
import store from './store';
import { datadogRum } from '@datadog/browser-rum';
import { ddAppId, ddClientToken, ddEnv, appVersion } from '@/shared/EnvConsts';
import { appId } from '@/shared/constants/global';

// Initialize Datadog RUM
datadogRum.init({
  applicationId: `${ddAppId}`,
  clientToken: `${ddClientToken}`,
  site: 'datadoghq.com',
  service: '{{application_id}}',
  env: `${ddEnv}`,
  version: `${appVersion}`,
  sessionSampleRate: 100,
  sessionReplaySampleRate: 100,
  startSessionReplayRecordingManually: true,
  trackUserInteractions: true,
  trackResources: true,
  trackLongTasks: true,
  defaultPrivacyLevel: 'mask-user-input',
  enableExperimentalFeatures: ['feature_flags'],
});

// Configure single-spa Vue lifecycles
const vueLifecycles: SingleSpaVueLifecycles = singleSpaVue({
  createApp,
  appOptions: {
    render() {
      return h(App, {});
    },
  },
  handleInstance(app) {
    app.use(router);
    app.use(store);
  },
});

// Style tag management for CSS isolation
let cachedStyleTag: string | null = null;

// Export single-spa lifecycle methods
export const bootstrap = vueLifecycles.bootstrap;

export const mount = async function (props: object) {
  // Restore cached styles on mount
  const existingStyleTag = document.getElementById(appId);
  if (existingStyleTag) {
    if (!cachedStyleTag) {
      cachedStyleTag = existingStyleTag.textContent;
    }
  } else if (cachedStyleTag) {
    const style = document.createElement('style');
    style.id = appId;
    style.textContent = cachedStyleTag;
    document.head.appendChild(style);
  }
  await vueLifecycles.mount(props);
};

export const unmount = async function (props: object) {
  // Clean up on unmount
  await vueLifecycles.unmount(props);
  const styleEl = document.getElementById(appId);
  if (styleEl) {
    styleEl.remove();
  }
};

// Standalone mode fallback (OPTIONAL - enhancement)
// Allows running the micro-frontend independently for development
if (!(window as any).singleSpaNavigate) {
  const app = createApp(App);
  app.use(router);
  app.use(store);
  app.mount('#app');
}
```

**Key Components**:
- **single-spa integration**: `singleSpaVue()` configuration
- **Lifecycle exports**: `bootstrap`, `mount`, `unmount`
- **Style management**: Caches and restores CSS between mounts
- **Standalone fallback**: Optional - mounts directly if not in single-spa

### 3. Environment Constants (`src/shared/EnvConsts.ts`)

```typescript
// Micro-frontend version - includes launcherAppUrl
export const ddAppId = import.meta.env.VITE_DATADOG_APPLICATION_ID || '';
export const ddClientToken = import.meta.env.VITE_DATADOG_CLIENT_TOKEN || '';
export const ddEnv = import.meta.env.VITE_DATADOG_ENV || 'local';
export const appVersion = import.meta.env.VITE_APP_VERSION || '0.0.0';
export const accessManagementBaseUrl = import.meta.env.VITE_ACCESS_MANAGEMENT_BASE_URL || '';
export const launcherAppUrl = import.meta.env.VITE_LAUNCHER_APP_URL || '';  // ← Required for micro-frontends
```

**Critical**: Must export `launcherAppUrl` for integration with single-spa root config

### 4. Vite Configuration (`vite.config.ts`)

```typescript
import { defineConfig } from 'vite';
import path from 'path';
import vue from '@vitejs/plugin-vue';
import cssInjectedByJsPlugin from 'vite-plugin-css-injected-by-js';
import svgLoader from 'vite-svg-loader';
import { appId } from './src/shared/constants/global';

export default defineConfig(({ mode, command }) => {
  const isBuild = command === 'build';

  return {
    plugins: [
      vue(),
      isBuild &&
        cssInjectedByJsPlugin({
          styleId: appId,  // CSS injection with app-specific ID
        }),
      svgLoader(),
    ].filter(Boolean),

    resolve: {
      alias: {
        '@': path.resolve(__dirname, 'src'),
      },
    },

    base: mode === 'development' ? '/' : './',

    server: {
      port: {{default_port}},
      proxy: {
        '^/{{api_base_path}}': {
          target: 'https://your-backend-server.com',
          changeOrigin: true,
          secure: false,
        },
      },
    },

    build: {
      target: 'esnext',
      lib: {
        entry: path.resolve(__dirname, 'src/main.ts'),
        formats: ['system'],  // ← SystemJS format for single-spa
        fileName: () => 'js/app.js',
      },
      rollupOptions: {
        output: {
          format: 'system',
          entryFileNames: 'js/app.js',
          exports: 'auto',
        },
      },
      sourcemap: true,
      emptyOutDir: true,
    },

    css: {
      preprocessorOptions: {
        scss: {
          additionalData: `@use "@/theme/" as *;`,
        },
      },
    },
  };
});
```

**Critical Settings**:
- `formats: ['system']` - SystemJS output for single-spa
- `cssInjectedByJsPlugin` - Injects CSS into JS for isolation
- `styleId: appId` - Unique style tag ID for caching

### 5. Package.json Dependencies

```json
{
  "dependencies": {
    "vue": "^3.5.13",
    "vue-router": "^4.6.3",
    "vuex": "^4.1.0",
    "single-spa-vue": "^3.0.1",  // ← Required for micro-frontends
    "axios": "^1.13.2",
    "@datadog/browser-rum": "^6.24.1"
  },
  "devDependencies": {
    "vite": "^6.3.5",
    "@vitejs/plugin-vue": "^6.0.2",
    "vite-plugin-css-injected-by-js": "^3.5.2",  // ← Required for CSS isolation
    "vite-svg-loader": "^5.1.0"
  }
}
```

**Critical Dependencies**:
- `single-spa-vue` - Single-spa integration for Vue 3
- `vite-plugin-css-injected-by-js` - CSS isolation plugin

### 6. Package.json Scripts

```json
{
  "scripts": {
    "dev": "vite preview --port {{default_port}}",
    "build:watch": "vite build --watch",
    "serve": "npm-run-all --parallel build:watch dev lint:init:watch",
    "build": "vite build",
    "serve:standalone": "vite serve --port {{default_port}} --mode standalone",  // ← Standalone mode
    "test:unit": "jest",
    "lint": "eslint --ext .js,.ts,.vue src"
  }
}
```

**Key Scripts**:
- `serve:standalone` - Runs app without single-spa (uses `.env.standalone`)
- `build:watch` - Rebuilds on file changes for development

### 7. Router Configuration

#### Option A: Hash History (Common for Micro-Frontends)
```typescript
import { createRouter, createWebHashHistory } from 'vue-router';

const router = createRouter({
  history: createWebHashHistory(),  // Hash-based routing
  routes,
});
```

**Pros**: Avoids path conflicts between micro-frontends  
**Cons**: URLs have `#` in them

#### Option B: HTML5 History with Base Path
```typescript
import { createRouter, createWebHistory } from 'vue-router';

const router = createRouter({
  history: createWebHistory('{{router_base_path}}'),  // e.g., '/inventory-manager'
  routes,
});
```

**Pros**: Clean URLs  
**Cons**: Requires proper base path configuration in single-spa

**Recommendation**: Hash history is safer for micro-frontends to avoid routing conflicts.

### 8. Index.html

```html
<!doctype html>
<html lang="">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1.0" />
    <link rel="icon" href="favicon.ico" />
    <title>{{application_name}}</title>
  </head>
  <body>
    <noscript>
      <strong>JavaScript required</strong>
    </noscript>
    <div id="app"></div>
  </body>
</html>
```

**Note**: Uses `#app` as mount point. Single-spa will mount the app when loaded.

## Workflow & Usage

### Development Workflow

#### 1. **Standalone Development** (Without Launcher)
```bash
npm run serve:standalone
```
- Uses `.env.standalone`
- App runs independently at `http://localhost:{{default_port}}`
- Good for isolated feature development

#### 2. **Integrated Development** (With Launcher)
```bash
npm run serve
```
- Uses `.env.local`
- Builds and serves via single-spa launcher
- Good for testing integration

### Build & Deploy

```bash
npm run build
```

**Output**:
- `dist/js/app.js` - SystemJS module
- Deployed to CDN or static server
- Loaded by single-spa root config

## Integration with Single-spa Root Config

The root config loads your micro-frontend:

```javascript
// In single-spa root config
{
  name: '@pdl-fulfillment-omni/inventory-manager',
  app: () => System.import('{{app_url}}/js/app.js'),
  activeWhen: ['/inventory-manager'],
}
```

## Style Isolation Strategy

### How It Works

1. **Build Time**: `vite-plugin-css-injected-by-js` injects CSS into JavaScript
2. **Mount Time**: CSS is added to `<style id="{{appId}}">` tag
3. **Cache**: Style tag content is cached in memory
4. **Unmount**: Style tag is removed from DOM
5. **Remount**: Cached styles are restored

### Why It's Needed

Prevents CSS conflicts when multiple micro-frontends are loaded simultaneously.

## Common Patterns

### Accessing Launcher URL

```typescript
import { launcherAppUrl } from '@/shared/EnvConsts';

// Navigate to another micro-frontend
const navigateToEquipment = () => {
  window.location.href = `${launcherAppUrl}#/equipment-manager`;
};
```

### Authentication Integration

```typescript
// Extract access token from query params (common pattern)
if (to.query.accessToken) {
  localStorage.setItem('accessToken', to.query.accessToken as string);
  delete to.query.accessToken;
  return next({ path: to.path, query: { ...to.query } });
}
```

### Cross-App Communication

```typescript
// Using window.postMessage or single-spa's messaging
window.dispatchEvent(new CustomEvent('inventory-updated', {
  detail: { itemId: '123' }
}));
```

## Validation Checklist

When generating a micro-frontend app, verify:

- [ ] `.env.local` includes `VITE_LAUNCHER_APP_URL`
- [ ] `.env.standalone` exists with `STANDALONE_SINGLE_SPA=true`
- [ ] `package.json` includes `single-spa-vue` dependency
- [ ] `main.ts` exports `bootstrap`, `mount`, `unmount`
- [ ] `main.ts` includes style tag caching logic
- [ ] `vite.config.ts` uses `formats: ['system']`
- [ ] `vite.config.ts` includes `vite-plugin-css-injected-by-js`
- [ ] `EnvConsts.ts` exports `launcherAppUrl`
- [ ] Router uses appropriate base path or hash history
- [ ] `serve:standalone` script exists in `package.json`

## Troubleshooting

### App doesn't load in single-spa
- Check SystemJS format in `vite.config.ts`
- Verify lifecycle exports in `main.ts`
- Confirm app registration in root config

### Styles missing after mount
- Verify `vite-plugin-css-injected-by-js` is installed
- Check style tag caching in `main.ts`
- Ensure unique `appId` for style tag

### Standalone mode not working
- Verify `.env.standalone` exists
- Check `serve:standalone` script
- Ensure fallback mount in `main.ts`

## Best Practices

1. **Use Hash History** - Avoids routing conflicts
2. **Unique App ID** - Ensures style isolation
3. **Environment Separation** - Keep dev and prod configs separate
4. **Test Both Modes** - Standalone and integrated
5. **Minimize Bundle Size** - Mark shared deps as external
6. **Version Consistency** - Align Vue versions across micro-frontends

## Summary

A micro-frontend application requires:
1. ✅ Single-spa lifecycle integration
2. ✅ SystemJS build format
3. ✅ Style tag management for CSS isolation
4. ✅ Dual environment files (`.env.local` + `.env.standalone`)
5. ✅ `VITE_LAUNCHER_APP_URL` for integration
6. ✅ `single-spa-vue` dependency
7. ✅ Standalone mode support (optional but recommended)

This configuration has been validated against production micro-frontend applications and follows best practices for single-spa micro frontend architecture.
