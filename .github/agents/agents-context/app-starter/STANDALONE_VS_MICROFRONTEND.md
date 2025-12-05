# Standalone vs Micro-Frontend Configuration Differences

## Overview

This document outlines the configuration differences between **standalone** and **micro-frontend** application types.

## Key Differences

### 1. Environment Files

#### Micro-Frontend (application_type: "micro-frontend")
Creates both development and standalone environment files:
- `.env.local` - For local development within single-spa
- `.env.standalone` - For running the micro-frontend independently

#### Standalone (application_type: "standalone")
Creates only:
- `.env.local` - Standard local development environment

**Reason**: Standalone apps don't need a separate standalone mode since they ARE standalone.

### 2. Environment Variables

#### Micro-Frontend Specific Variables
```bash
# .env.local (micro-frontend)
VITE_LAUNCHER_APP_URL=http://localhost:9000  # single-spa root config
VITE_ACCESS_MANAGEMENT_BASE_URL=http://localhost:8088
```

```bash
# .env.standalone (micro-frontend only)
VITE_LAUNCHER_APP_URL=  # Empty - runs without single-spa
```

#### Standalone Variables
```bash
# .env.local (standalone)
# NO VITE_LAUNCHER_APP_URL - not needed
# Standard app variables only
```

### 3. main.ts Entry Point

#### Micro-Frontend main.ts
```typescript
import singleSpaVue, { SingleSpaVueLifecycles } from 'single-spa-vue';

// single-spa lifecycle exports
export const bootstrap = vueLifecycles.bootstrap;
export const mount = async function (props: object) { ... };
export const unmount = async function (props: object) { ... };

// Standalone fallback mode
if (!(window as any).singleSpaNavigate) {
  const app = createApp(App);
  app.use(router);
  app.use(store);
  app.mount('#app');
}
```

#### Standalone main.ts
```typescript
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';

// Direct mount - no single-spa lifecycle
const app = createApp(App);
app.use(router);
app.use(store);
app.mount('#app');
```

**No single-spa imports or lifecycle methods needed.**

### 4. package.json Dependencies

#### Micro-Frontend
```json
{
  "dependencies": {
    "single-spa-vue": "^3.0.1"
  }
}
```

#### Standalone
```json
{
  "dependencies": {
    // NO single-spa-vue dependency
  }
}
```

### 5. Vite Configuration

#### Micro-Frontend (vite_build_format: "system")
```typescript
export default defineConfig({
  build: {
    lib: {
      formats: ['system'],  // SystemJS for single-spa
      // ...
    },
  },
});
```

#### Standalone (vite_build_format: "es")
```typescript
export default defineConfig({
  build: {
    lib: {
      formats: ['es'],  // ES modules for standalone
      // ...
    },
  },
});
```

### 6. index.html

#### Micro-Frontend
```html
<!-- Minimal - loaded by single-spa root config -->
<div id="single-spa-application:{{application_name}}"></div>
```

#### Standalone
```html
<!-- Standard Vue app structure -->
<div id="app"></div>
```

### 7. Router Base Path

#### Micro-Frontend
```typescript
const router = createRouter({
  history: createWebHistory('{{router_base_path}}'),  // e.g., '/omni-inventory'
  // ...
});
```

#### Standalone
```typescript
const router = createRouter({
  history: createWebHistory('/'),  // Usually root path
  // ...
});
```

### 8. EnvConsts.ts

#### Micro-Frontend
```typescript
export const launcherAppUrl = import.meta.env.VITE_LAUNCHER_APP_URL || '';
export const accessManagementBaseUrl = import.meta.env.VITE_ACCESS_MANAGEMENT_BASE_URL || '';
// ... other vars
```

#### Standalone
```typescript
// NO launcherAppUrl export
export const accessManagementBaseUrl = import.meta.env.VITE_ACCESS_MANAGEMENT_BASE_URL || '';
// ... other vars
```

### 9. Docker & Nginx Configuration

#### Both Types
Docker and Nginx configurations are similar, but standalone may have simpler routing.

## Configuration Matrix

| Feature                    | Micro-Frontend                | Standalone                    |
|----------------------------|-------------------------------|-------------------------------|
| `.env.local`               | ✅ Yes                        | ✅ Yes                        |
| `.env.standalone`          | ✅ Yes                        | ❌ No                         |
| `single-spa-vue` dependency| ✅ Yes                        | ❌ No                         |
| single-spa lifecycle       | ✅ Yes (export bootstrap, etc)| ❌ No                         |
| Fallback standalone mode   | ✅ Yes (in main.ts)           | ❌ Not needed                 |
| `VITE_LAUNCHER_APP_URL`    | ✅ Required                   | ❌ Not used                   |
| Build format               | `system` (SystemJS)           | `es` (ES modules)             |
| Router base path           | `/{{app-name}}` (scoped)      | `/` (typically root)          |
| index.html mount point     | `#single-spa-application:...` | `#app`                        |
| Style tag management       | ✅ Yes (mount/unmount)        | ❌ Not needed                 |

## Agent Implementation

### Decision Logic

```javascript
if (userConfig.application_type === 'standalone') {
  // Standalone configuration
  skipFile('.env.standalone');
  skipDependency('single-spa-vue');
  skipEnvVar('VITE_LAUNCHER_APP_URL');
  useTemplate('main-standalone.ts');
  setIndexMountPoint('#app');
  
} else {
  // Micro-frontend configuration
  createFile('.env.standalone');
  includeDependency('single-spa-vue');
  includeEnvVar('VITE_LAUNCHER_APP_URL');
  useTemplate('main-microfrontend.ts');
  setIndexMountPoint('#single-spa-application:{{application_name}}');
}
```

## Files to Update

Based on `application_type`, the agent should conditionally generate:

### 1. `.env.standalone`
- **Micro-frontend**: Create this file
- **Standalone**: Skip this file

### 2. `src/main.ts`
- **Micro-frontend**: Use template with single-spa lifecycle
- **Standalone**: Use simplified template without single-spa

### 3. `src/shared/EnvConsts.ts`
- **Micro-frontend**: Include `launcherAppUrl` export
- **Standalone**: Exclude `launcherAppUrl` export

### 4. `package.json`
- **Micro-frontend**: Include `single-spa-vue` in dependencies
- **Standalone**: Exclude `single-spa-vue`

### 5. `public/index.html`
- **Micro-frontend**: Use `#single-spa-application:{{application_name}}`
- **Standalone**: Use `#app`

### 6. Package.json Scripts
- **Both**: Same scripts, but standalone won't use `.env.standalone`

## Example Configurations

### Micro-Frontend Example
```json
{
  "application_type": "micro-frontend",
  "router_base_path": "/omni-inventory-manager",
  "enable_single_spa": true
}
```

Generates:
- ✅ `.env.standalone` file
- ✅ `single-spa-vue` dependency
- ✅ `VITE_LAUNCHER_APP_URL` in EnvConsts
- ✅ single-spa lifecycle in main.ts

### Standalone Example
```json
{
  "application_type": "standalone",
  "router_base_path": "/",
  "enable_single_spa": false
}
```

Generates:
- ❌ No `.env.standalone` file
- ❌ No `single-spa-vue` dependency
- ❌ No `VITE_LAUNCHER_APP_URL` in EnvConsts
- ✅ Simple main.ts without single-spa

## Validation Rules

The agent should validate:

```javascript
// If standalone, these should be false/empty
if (application_type === 'standalone') {
  assert(enable_single_spa === false, 
    'Standalone apps cannot use single-spa');
  assert(router_base_path === '/' || confirm, 
    'Standalone apps typically use root path');
}

// If micro-frontend, these should be true
if (application_type === 'micro-frontend') {
  assert(enable_single_spa === true, 
    'Micro-frontend apps require single-spa');
  assert(router_base_path !== '/', 
    'Micro-frontend apps should have scoped base path');
}
```

## Summary

The key difference you identified—**no `.env.standalone` file for standalone apps**—is correct and extends to several other areas:

1. ❌ No `.env.standalone` file
2. ❌ No `single-spa-vue` dependency
3. ❌ No `VITE_LAUNCHER_APP_URL` environment variable
4. ✅ Simplified `main.ts` (no lifecycle methods)
5. ✅ Different `index.html` mount point
6. ✅ Different build format (ES vs SystemJS)

All these differences should be automatically configured by the agent based on the `application_type` setting.

## Validation Against Real Project

This configuration has been validated against the real standalone project: **`pdl-fulfillment-cf-launcher-web`**

### Confirmed Patterns ✅

| Feature | Template (Vite) | Real Project (Vue CLI) | Status |
|---------|----------------|------------------------|--------|
| `.env.standalone` | ❌ Not created | ❌ Does not exist | ✅ Match |
| `single-spa-vue` | ❌ Not included | ❌ Not in package.json | ✅ Match |
| main.ts lifecycle | ❌ No exports | ❌ Simple mount only | ✅ Match |
| Mount point | `#app` | `#app` | ✅ Match |
| Datadog RUM | ✅ Included | ✅ Included | ✅ Match |
| State management | Vuex/Pinia | Vuex | ✅ Match |
| Testing | Jest/Vitest | Jest | ✅ Match |

### Tool Differences (Expected)

The real project uses **Vue CLI** while our template uses **Vite**. This results in expected differences:

| Aspect | Template (Vite) | Real Project (Vue CLI) |
|--------|----------------|------------------------|
| Build tool | Vite | Vue CLI / Webpack |
| Config file | `vite.config.ts` | `vue.config.js` |
| Env prefix | `VITE_*` | `VUE_APP_*` |
| Env access | `import.meta.env` | `process.env` |
| Router history | `createWebHistory` | `createWebHashHistory` |

These differences are **expected and correct** - both are valid standalone patterns. Our template targets the **modern Vite ecosystem** which is recommended for new projects.

### Conclusion

✅ **Our standalone implementation is CORRECT** for Vite-based projects.
✅ **All key patterns match** the real standalone project.
✅ **Tooling differences** (Vite vs Vue CLI) are expected and intentional.

The standalone configuration in this template accurately represents modern standalone Vue 3 applications.
