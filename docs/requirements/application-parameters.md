## Application Parameters

This document describes the application-specific parameters used by the app-starter agent when generating Vue 3 Vite applications.

**Note**: For the complete questionnaire and configuration flow, see [`.github/agents/agents-context/app-starter/.copilot-instructions.md`](../../.github/agents/agents-context/app-starter/.copilot-instructions.md).

## Parameter Summary

The agent collects the following parameters from the user:

1. `application_name` - Application name in kebab-case
2. `project_scope` - NPM scope/organization
3. `router_base_path` - Base path for Vue Router
4. `api_base_path` - Base API path for backend proxying
5. `default_port` - Development server port
6. `application_type` - `micro-frontend` or `standalone`
7. `include_component_library` - Whether to include component library
8. `github_token` - GitHub PAT (only if component library is included)
9. `vue_api_pattern` - `composition-api` or `options-api`
10. `state_management` - `pinia` or `vuex`
11. `test_framework` - `vitest` or `jest`
12. `use_latest_versions` - `yes` or `no`

### Auto-Configured Parameters

The following are automatically set based on user selections:
- `vite_build_format`: Set based on `application_type` (`micro-frontend` → `"system"`, `standalone` → `"es"`)
- Package versions: Managed via `versions.json` or fetched from npm based on `use_latest_versions`

### Version Management

**IMPORTANT**: Package versions are managed centrally via `agents/agents-context/app-starter/skills/package-json/versions.json`.

**How it works**:
- All package versions defined in `versions.json` (in package-json skill)
- Use `"latest"` to auto-fetch from npm registry at generation time
- Use specific versions (e.g., `"^6.3.5"`) to pin for stability
- The version-resolver skill resolves versions during generation

**When user requests "latest configuration"**:
1. Load `versions.json` from package-json skill
2. For packages marked as `"latest"`: Run `npm view <package> version`
3. Apply caret prefix: `3.5.13` → `^3.5.13`
4. Replace placeholders in templates: `{{vue_version}}` → `^3.5.13`

**Examples**:
```json
// All latest (development)
{
  "core": { "vue": "latest", "vite": "latest" }
}

// Pinned (production)
{
  "core": { "vue": "^3.5.13", "vite": "^6.3.5" }
}

// Mixed (hybrid)
{
  "core": { "vue": "latest", "typescript": "^5.7.3" }
}
```

**Key files**:
- `skills/package-json/SKILL.md` - Complete documentation with embedded version configuration
- `skills/package-json/reference/latest-versions.md` - Latest versions template

**DO NOT hardcode versions** in skills/examples. Use placeholders: `{{vue_version}}`, `{{vite_version}}`, etc.

### Static Parameters (Pre-configured)

The app will require latest Vue, TypeScript, and other libraries versions so verify whether the current node and npm versions are up to date before generating the project. If not, check whether the current node and npm versions are compatible with the latest stable versions of Vue, or inform the user to update their environment.

### Auto-Derived Parameters

The following parameters should be automatically derived from the user's inputs:

- `main_component_name`: Derived from `application_name` by removing dashes and using PascalCase (e.g., `omni-inventory-manager-web` → `OmniInventoryManagerWeb`)
  - Used in: Component names, class names

- `application_id`: Same as `application_name` in kebab-case, used for style tag IDs and CSS isolation
  - Used in: Style tag IDs in main.ts, CSS isolation, Datadog service name

- `service_name`: Same as `application_name` in kebab-case, used for service identification in monitoring
  - Used in: Datadog RUM configuration, logging

## Environment Variables

The following environment variables should be configured in `.env.local` (gitignored) and documented in README.md:

### Required Environment Variables

- `NODE_ENV`: Environment mode (`development`, `production`, `test`)
- `VITE_ACCESS_MANAGEMENT_BASE_URL`: URL for access management service
- `VITE_LAUNCHER_APP_URL`: URL for the launcher/root application (single-spa)
- `VITE_DATADOG_ENV`: Datadog environment identifier (`local`, `dev`, `staging`, `prod`)
- `VITE_DATADOG_APPLICATION_ID`: Datadog RUM application ID
- `VITE_DATADOG_CLIENT_TOKEN`: Datadog RUM client token
- `VITE_APP_VERSION`: Application version (from package.json)

### Optional Environment Variables

- `VITE_API_BASE_URL`: Override for API base URL (if not using proxy)
- `VITE_ENABLE_MOCK`: Enable mock API responses for local development
