# Environment Files Skill

## Purpose
Generate environment files for local development.

## Output
Create the files:
- `.env.local.example` (template for developers)
- `.env.standalone` (for standalone mode)

## Template: .env.local.example

```bash
NODE_ENV=development
VITE_ACCESS_MANAGEMENT_BASE_URL=https://your-access-manager-url.com
VITE_LAUNCHER_APP_URL=https://your-launcher-url.com
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=your-datadog-app-id
VITE_DATADOG_CLIENT_TOKEN=your-datadog-client-token
VITE_APP_VERSION=0.1.0
```

## Template: .env.standalone

```bash
NODE_ENV=development
VITE_ACCESS_MANAGEMENT_BASE_URL=http://localhost:8080
VITE_LAUNCHER_APP_URL=http://localhost:9000
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=standalone-app-id
VITE_DATADOG_CLIENT_TOKEN=standalone-client-token
VITE_APP_VERSION=0.1.0
```

## Notes
- .env.local.example serves as a template for developers
- Developers should copy this to .env.local and fill in actual values
- .env.standalone is for running the app without the launcher
- .env.local should be in .gitignore
