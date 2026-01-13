# App Starter Configuration

This directory contains configuration files for the `@app-starter` agent.

## Quick Start

1. **Copy the example configuration:**
   ```bash
   cp config/app-config.example.json config/app-config.json
   ```

2. **Edit `app-config.json` with your values:**
   ```json
   {
     "application_name": "my-app-name",
     "project_scope": "@my-organization",
     "router_base_path": "/my-app/",
     "api_base_path": "/api/my-service",
     "default_port": 3000,
     "application_type": "micro-frontend",
     "include_component_library": "yes",
     "vue_api_pattern": "composition-api",
     "state_management": "pinia",
     "test_framework": "vitest",
     "use_latest_versions": "yes"
   }
   ```

3. **Run the agent:**
   ```
   @app-starter generate a new Vue 3 application
   ```

## Files

- **`app-config.example.json`** - Template with all available options and help text (committed to repo)
- **`app-config.schema.json`** - JSON schema for validation and IDE autocomplete
- **`app-config.json`** - Your actual configuration (typically not committed; add `config/app-config.json` to your `.gitignore` to keep it local)

## Configuration Parameters

### Required Parameters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `application_name` | string | Application name in kebab-case | `"omni-inventory-manager-web"` |
| `project_scope` | string | NPM scope/organization | `"@royalaholddelhaize"` |
| `router_base_path` | string | Base path for Vue Router | `"/omni-inventory-manager/"` |
| `api_base_path` | string | Base API path for backend | `"/api/inventory"` |
| `default_port` | number | Development server port | `3000` |
| `application_type` | enum | `"micro-frontend"` or `"standalone"` | `"micro-frontend"` |
| `include_component_library` | enum | `"yes"` or `"no"` | `"yes"` |
| `vue_api_pattern` | enum | `"composition-api"` or `"options-api"` | `"composition-api"` |
| `state_management` | enum | `"pinia"` or `"vuex"` | `"pinia"` |
| `test_framework` | enum | `"vitest"` or `"jest"` | `"vitest"` |
| `use_latest_versions` | enum | `"yes"` or `"no"` | `"yes"` |

### Auto-Derived Parameters

The agent automatically derives these from your configuration:

- **`main_component_name`**: PascalCase version of `application_name`
  - Example: `omni-inventory-manager-web` → `OmniInventoryManagerWeb`
  
- **`application_id`**: Same as `application_name` (kebab-case)
  - Used for: Style tag IDs, CSS isolation, Datadog service name
  
- **`vite_build_format`**: Set based on `application_type`
  - `"micro-frontend"` → `"system"` (SystemJS for single-spa)
  - `"standalone"` → `"es"` (ES modules)

## Validation

Your IDE should provide autocomplete and validation if it supports JSON Schema.

You can also validate manually:
```bash
# Using ajv-cli (install: npm install -g ajv-cli)
ajv validate -s config/app-config.schema.json -d config/app-config.json
```

## Tips

- **Application Name**: Use kebab-case (lowercase with hyphens)
- **Router Base Path**: Must start and end with `/` (e.g., `/my-app/`)
- **Port**: Choose a unique port to avoid conflicts with other apps
- **Component Library**: Requires GitHub token if set to `"yes"` (see [docs](../docs/requirements/application-parameters.md))
- **Latest Versions**: `"yes"` fetches from npm, `"no"` uses pinned versions from `versions.json`

## Examples

### Micro-Frontend Application (Recommended)
```json
{
  "application_name": "omni-inventory-manager-web",
  "project_scope": "@royalaholddelhaize",
  "router_base_path": "/omni-inventory-manager/",
  "api_base_path": "/api/inventory",
  "default_port": 3000,
  "application_type": "micro-frontend",
  "include_component_library": "yes",
  "vue_api_pattern": "composition-api",
  "state_management": "pinia",
  "test_framework": "vitest",
  "use_latest_versions": "yes"
}
```

### Standalone Application
```json
{
  "application_name": "my-standalone-app",
  "project_scope": "@my-company",
  "router_base_path": "/",
  "api_base_path": "/api",
  "default_port": 5173,
  "application_type": "standalone",
  "include_component_library": "no",
  "vue_api_pattern": "composition-api",
  "state_management": "pinia",
  "test_framework": "vitest",
  "use_latest_versions": "yes"
}
```

### Legacy Configuration (Options API + Vuex + Jest)
```json
{
  "application_name": "legacy-app",
  "project_scope": "@my-company",
  "router_base_path": "/legacy-app/",
  "api_base_path": "/api/legacy",
  "default_port": 8080,
  "application_type": "standalone",
  "include_component_library": "no",
  "vue_api_pattern": "options-api",
  "state_management": "vuex",
  "test_framework": "jest",
  "use_latest_versions": "no"
}
```

## Troubleshooting

**Q: IDE doesn't show autocomplete?**
- Ensure the `$schema` property is at the top of `app-config.json`
- Try restarting your IDE

**Q: Validation errors?**
- Check that all required fields are filled
- Verify enum values match exactly (case-sensitive)
- Ensure `router_base_path` starts and ends with `/`
- Ensure `project_scope` starts with `@`

**Q: Where is `app-config.json`?**
- It's gitignored by design - copy from `app-config.example.json`
- Each user creates their own local configuration

## Related Documentation

- [Application Parameters](../docs/requirements/application-parameters.md) - Detailed parameter reference
- [App Starter Agent](../.github/agents/app-starter.agent.md) - Agent documentation
- [Agent Instructions](../.github/agents/app-starter/agents-context/.copilot-instructions.md) - Complete workflow
