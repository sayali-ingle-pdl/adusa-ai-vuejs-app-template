# Main Entry Skill

## Purpose
Generate the `src/main.ts` file. Creates different versions based on application type:
- **Micro-frontend**: Includes single-spa lifecycle methods and style tag management
- **Standalone**: Simple Vue app initialization

## Input Parameters
- `application_type`: "micro-frontend" or "standalone"
- `application_id`: The application ID for style tag (micro-frontend only)
- `enable_datadog`: Boolean to include Datadog RUM
- `state_management`: "vuex" or "pinia"

## Output
Create the file: `src/main.ts`

## Template: Micro-Frontend

```typescript
import { h, createApp } from 'vue';
import singleSpaVue, { SingleSpaVueLifecycles } from 'single-spa-vue';
import App from './App.vue';
import router from './router';
import store from './store';
import { datadogRum } from '@datadog/browser-rum';
import { ddAppId, ddClientToken, ddEnv, appVersion } from '@/shared/EnvConsts';
import { appId } from '@/shared/constants/global';

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

let cachedStyleTag: string | null = null;
export const bootstrap = vueLifecycles.bootstrap;
export const mount = async function (props: object) {
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
  await vueLifecycles.unmount(props);
  const styleEl = document.getElementById(appId);
  if (styleEl) {
    styleEl.remove();
  }
};

// Standalone mode: Mount app directly if not running in single-spa
// This allows the micro-frontend to run independently for development/testing
if (!(window as any).singleSpaNavigate) {
  const app = createApp(App);
  app.use(router);
  app.use(store);
  app.mount('#app');
}
```

## Template: Standalone

```typescript
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store'; // or pinia store
import { datadogRum } from '@datadog/browser-rum';
import { ddAppId, ddClientToken, ddEnv, appVersion } from '@/shared/EnvConsts';

// Initialize Datadog RUM (if enabled)
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

// Standard Vue app initialization
const app = createApp(App);
app.use(router);
app.use(store);
app.mount('#app');
```

## Notes

### Micro-Frontend Template
- Exports bootstrap, mount, and unmount for single-spa
- Caches and restores style tag for CSS isolation
- Initializes Datadog RUM for monitoring
- Sets up Vue Router and Vuex/Pinia store
- **Standalone Fallback Mode**: Automatically detects if running outside single-spa context
  - Checks for `window.singleSpaNavigate` to determine if single-spa is present
  - If not present, mounts the app directly to `#app` element
  - Enables running the micro-frontend in standalone mode for development and testing

### Standalone Template
- No single-spa imports or lifecycle methods
- Direct app initialization and mount
- Simple, straightforward entry point
- Still includes Datadog RUM if enabled
- Mounts to `#app` element

## Conditional Logic

```javascript
if (application_type === 'standalone') {
  // Use standalone template
  // - No single-spa imports
  // - No lifecycle exports
  // - No style tag management
  // - Simple createApp and mount
} else {
  // Use micro-frontend template
  // - Include single-spa-vue
  // - Export lifecycle methods
  // - Style tag caching
  // - Standalone fallback
}

// Datadog inclusion based on enable_datadog flag
if (!enable_datadog) {
  // Remove datadogRum.init() block from both templates
}

// Store import based on state_management
if (state_management === 'pinia') {
  // import store from './stores'  (Pinia)
} else {
  // import store from './store'   (Vuex)
}
```
