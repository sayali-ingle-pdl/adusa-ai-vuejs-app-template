# Environment Standalone Skill

## Purpose
Generate `.env.standalone` file for running micro-frontend applications in standalone mode (without single-spa launcher).

**Note**: This file should ONLY be created for micro-frontend applications (`application_type: "micro-frontend"`). Standalone applications do NOT need this file.

## Input Parameters
- `application_type`: Should be "micro-frontend" to generate this file

## Output
Create the file: `.env.standalone` (only if `application_type === "micro-frontend"`)

## Conditional Logic

```javascript
if (application_type === 'standalone') {
  // SKIP THIS FILE - standalone apps don't need .env.standalone
  return;
}

// Only create for micro-frontend apps
if (application_type === 'micro-frontend') {
  // Generate .env.standalone
}
```

## Template

```bash
STANDALONE_SINGLE_SPA=true
```

## Notes
- **Micro-Frontend Only**: This file is only relevant for micro-frontend applications
- **Standalone Apps**: Do NOT create this file for standalone applications
- `.env.standalone` enables standalone mode for development without the launcher
- When `STANDALONE_SINGLE_SPA=true`, the micro-frontend app runs independently
- Useful for local development and testing without single-spa dependencies
- This file can be committed to the repository (no secrets)
- Use with `npm run dev:standalone` or similar script (if using Vite modes)
- The application will mount directly without waiting for single-spa lifecycle

## Why Standalone Apps Don't Need This

Standalone applications already run independently by default. They don't have single-spa integration, so there's no need for a separate "standalone mode" - they ARE standalone. Creating this file for standalone apps would be redundant and confusing.
