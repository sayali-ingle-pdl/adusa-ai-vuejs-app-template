# Main Entry Skill

## Purpose
Generate the `src/main.ts` file with single-spa lifecycle methods and Datadog RUM integration.

## Input Parameters
- `application_id`: The application ID for style tag

## Output
Create the file: `src/main.ts`

## Template

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
```

## Notes
- Exports bootstrap, mount, and unmount for single-spa
- Caches and restores style tag for CSS isolation
- Initializes Datadog RUM for monitoring
- Sets up Vue Router and Vuex store
