# Environment Local Configuration Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains the template and detailed examples.

## Basic Template

```bash
NODE_ENV=development
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=<>
VITE_DATADOG_CLIENT_TOKEN=<>
```

## What Each Variable Does

### 1. `NODE_ENV=development`
- Sets the Node.js environment mode
- Values: `development`, `production`, `test`
- Affects build optimizations and debugging features
- In development mode: enables hot module replacement, detailed error messages
- In production mode: enables minification, tree-shaking, optimizations

### 2. `VITE_DATADOG_ENV=local`
- Identifies the environment in Datadog RUM (Real User Monitoring)
- Values: `local`, `dev`, `staging`, `prod`
- Helps filter and organize monitoring data by environment
- Prevents local development data from polluting production metrics

### 3. `VITE_DATADOG_APPLICATION_ID=<>`
- Unique identifier for your application in Datadog
- Obtained from Datadog RUM application settings
- Required for Datadog RUM to function
- Example: `a1b2c3d4-e5f6-g7h8-i9j0-k1l2m3n4o5p6`

### 4. `VITE_DATADOG_CLIENT_TOKEN=<>`
- Authentication token for Datadog RUM client
- Obtained from Datadog RUM application settings
- Required to send monitoring data to Datadog
- Example: `pub1234567890abcdefghijklmnopqrst`

## How to Get Datadog Credentials

### Step 1: Access Datadog
1. Log in to your Datadog account
2. Navigate to **UX Monitoring** → **RUM Applications**

### Step 2: Create or Select Application
1. Create a new RUM application or select existing one
2. Choose **Browser** as the application type

### Step 3: Get Application ID and Client Token
1. In the application settings, find:
   - **Application ID** (applicationId)
   - **Client Token** (clientToken)
2. Copy these values to your `.env.local` file

## Complete .env.local Example

```bash
# Node Environment
NODE_ENV=development

# Datadog RUM Configuration
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=a1b2c3d4-e5f6-g7h8-i9j0-k1l2m3n4o5p6
VITE_DATADOG_CLIENT_TOKEN=pub1234567890abcdefghijklmnopqrst

# Optional: Additional Configuration
VITE_APP_VERSION=0.1.0
VITE_ACCESS_MANAGEMENT_BASE_URL=https://your-access-manager-url.com
VITE_LAUNCHER_APP_URL=https://your-launcher-url.com
VITE_API_BASE_URL=http://localhost:3000
```

## Environment-Specific Configurations

### Local Development (.env.local)
```bash
NODE_ENV=development
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=<local-app-id>
VITE_DATADOG_CLIENT_TOKEN=<local-client-token>
```

### Development Server (.env.development)
```bash
NODE_ENV=development
VITE_DATADOG_ENV=dev
VITE_DATADOG_APPLICATION_ID=<dev-app-id>
VITE_DATADOG_CLIENT_TOKEN=<dev-client-token>
```

### Staging (.env.staging)
```bash
NODE_ENV=production
VITE_DATADOG_ENV=staging
VITE_DATADOG_APPLICATION_ID=<staging-app-id>
VITE_DATADOG_CLIENT_TOKEN=<staging-client-token>
```

### Production (.env.production)
```bash
NODE_ENV=production
VITE_DATADOG_ENV=prod
VITE_DATADOG_APPLICATION_ID=<prod-app-id>
VITE_DATADOG_CLIENT_TOKEN=<prod-client-token>
```

## Accessing Environment Variables in Code

### In Vue Components or TypeScript
```typescript
// Access environment variables
const nodeEnv = import.meta.env.MODE; // 'development' or 'production'
const datadogEnv = import.meta.env.VITE_DATADOG_ENV;
const appId = import.meta.env.VITE_DATADOG_APPLICATION_ID;
const clientToken = import.meta.env.VITE_DATADOG_CLIENT_TOKEN;

// Type-safe access (recommended)
// In src/shared/EnvConsts.ts
export const EnvConsts = {
  NODE_ENV: import.meta.env.MODE,
  DATADOG_ENV: import.meta.env.VITE_DATADOG_ENV,
  DATADOG_APP_ID: import.meta.env.VITE_DATADOG_APPLICATION_ID,
  DATADOG_CLIENT_TOKEN: import.meta.env.VITE_DATADOG_CLIENT_TOKEN,
};
```

### Vite Environment Variable Rules
1. **Prefix Required**: Only variables prefixed with `VITE_` are exposed to client code
2. **NODE_ENV**: Automatically set by Vite, don't need VITE_ prefix
3. **Build Time**: Variables are replaced at build time (static values only)
4. **Type Safety**: Define types in `src/vite-env.d.ts`

## Security Best Practices

### 1. Never Commit .env.local
Add to `.gitignore`:
```
.env.local
.env.*.local
```

### 2. Provide .env.example Template
```bash
NODE_ENV=development
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=<replace-with-your-app-id>
VITE_DATADOG_CLIENT_TOKEN=<replace-with-your-client-token>
```

### 3. Document Required Variables
Create a README section listing all required environment variables:
- Variable name
- Purpose
- Where to get the value
- Example format (without real values)

### 4. Use Different Credentials Per Environment
- Never reuse production credentials in local/dev environments
- Create separate Datadog applications for each environment
- Rotate tokens regularly

## Troubleshooting

### Variables Not Loading
- Ensure file is named exactly `.env.local`
- Restart dev server after changing env files
- Check variable names have `VITE_` prefix (except NODE_ENV)
- Verify file is in project root directory

### Undefined Environment Variables
```typescript
// Add type definitions in src/vite-env.d.ts
interface ImportMetaEnv {
  readonly VITE_DATADOG_ENV: string;
  readonly VITE_DATADOG_APPLICATION_ID: string;
  readonly VITE_DATADOG_CLIENT_TOKEN: string;
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

### Datadog Not Initializing
- Verify Application ID and Client Token are correct
- Check network tab for failed requests to Datadog
- Ensure Datadog RUM is properly initialized in `main.ts`
- Confirm environment variables are loaded: `console.log(import.meta.env)`

## File Priority

Vite loads environment files in this order (later files override earlier):
1. `.env` - Loaded in all cases
2. `.env.local` - Loaded in all cases, ignored by git
3. `.env.[mode]` - Only loaded in specified mode (e.g., `.env.production`)
4. `.env.[mode].local` - Only loaded in specified mode, ignored by git

## Best Practices

1. **Keep It Minimal**: Only include variables needed for the application
2. **Document Everything**: Add comments explaining each variable's purpose
3. **Provide Examples**: Include `.env.example` with placeholder values
4. **Validate on Startup**: Check required variables are set when app initializes
5. **Environment-Specific Values**: Use different values for local, dev, staging, prod
6. **Never Log Secrets**: Avoid logging token values even in development
