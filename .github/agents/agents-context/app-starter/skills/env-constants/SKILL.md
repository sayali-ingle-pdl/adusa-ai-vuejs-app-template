# Environment Constants Skill

## Purpose
Generate the `src/shared/EnvConsts.ts` file for accessing environment variables with type safety.

## Input Parameters
- `application_type`: "micro-frontend" or "standalone"
- `enable_datadog`: Boolean to include Datadog variables

## Output
Create the file: `src/shared/EnvConsts.ts`

## Template: Micro-Frontend

```typescript
// Environment variable exports with type safety and defaults

export const ddAppId = import.meta.env.VITE_DATADOG_APPLICATION_ID || '';
export const ddClientToken = import.meta.env.VITE_DATADOG_CLIENT_TOKEN || '';
export const ddEnv = import.meta.env.VITE_DATADOG_ENV || 'local';
export const appVersion = import.meta.env.VITE_APP_VERSION || '0.0.0';
export const accessManagementBaseUrl = import.meta.env.VITE_ACCESS_MANAGEMENT_BASE_URL || '';
export const launcherAppUrl = import.meta.env.VITE_LAUNCHER_APP_URL || '';
```

## Template: Standalone

```typescript
// Environment variable exports with type safety and defaults

export const ddAppId = import.meta.env.VITE_DATADOG_APPLICATION_ID || '';
export const ddClientToken = import.meta.env.VITE_DATADOG_CLIENT_TOKEN || '';
export const ddEnv = import.meta.env.VITE_DATADOG_ENV || 'local';
export const appVersion = import.meta.env.VITE_APP_VERSION || '0.0.0';
export const accessManagementBaseUrl = import.meta.env.VITE_ACCESS_MANAGEMENT_BASE_URL || '';
// No launcherAppUrl for standalone apps
```

## Notes
- Wraps Vite's import.meta.env for type safety
- Provides default values for all environment variables
- Makes environment variables easily mockable in tests
- **Micro-Frontend**: Includes `launcherAppUrl` for single-spa root config
- **Standalone**: Excludes `launcherAppUrl` as it's not needed
- Datadog variables can be conditionally excluded if `enable_datadog` is false
