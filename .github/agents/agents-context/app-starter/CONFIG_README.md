# Application Configuration Guide

This directory provides configuration options for the Vue 3 Vite application generator.

## Configuration Methods

Users can provide application parameters in two ways:

### Method 1: Interactive Terminal Prompts (Default)
Run the agent and answer the prompts interactively in the terminal.

### Method 2: Configuration File (config.json)
Create a `config.json` file in the project root with your application parameters. The agent will read values from this file and skip prompting for those values.

## Configuration Files

### config.schema.json
JSON Schema definition for the configuration file. This provides:
- Type validation for all configuration options
- Pattern validation (e.g., kebab-case for application names)
- Default values
- Documentation for each field

### config.example.json
Example configuration file showing all available options with sample values. Copy this file to `config.json` in your project root and modify the values.

## Usage

### Option 1: Use Example as Template
```bash
# Copy the example to your project root
cp .github/agents/agents-context/app-starter/config.example.json config.json

# Edit config.json with your values
# Then run the agent
```

### Option 2: Create Your Own config.json
Create a `config.json` file in your project root with at minimum the required fields:

```json
{
  "application_name": "my-app-web",
  "project_scope": "@myorg",
  "router_base_path": "/my-app",
  "api_base_path": "/api",
  "default_port": 8080,
  "application_type": "micro-frontend",
  "test_framework": "vitest",
  "state_management": "pinia"
}
```

### Option 3: Hybrid Approach
Provide some values in `config.json` and the agent will only prompt for missing required values:

```json
{
  "application_name": "my-app-web",
  "project_scope": "@myorg",
  "test_framework": "vitest",
  "state_management": "pinia"
}
```

The agent will prompt for the missing fields: `router_base_path`, `api_base_path`, `default_port`, and `application_type`.

## Configuration Fields

### Required Fields

1. **application_name** (string)
   - Format: kebab-case (e.g., `omni-inventory-manager-web`)
   - Used in: package.json, vite.config.ts, README.md, index.html

2. **project_scope** (string)
   - Format: npm scope with @ prefix (e.g., `@pdl-fulfillment-omni`)
   - Used in: package.json name field

3. **router_base_path** (string)
   - Format: path starting with / (e.g., `/omni-inventory-manager`)
   - Used in: Vue Router configuration

4. **api_base_path** (string)
   - Format: path starting with / (e.g., `/omni-access-manager`, `/api`)
   - Used in: Vite proxy configuration, axios base URL

5. **default_port** (integer)
   - Range: 1024-65535 (e.g., `8089`)
   - Used in: vite.config.ts, package.json scripts

6. **application_type** (enum)
   - Options: `"micro-frontend"` or `"standalone"`
   - **`"micro-frontend"`**: For applications built with single-spa micro frontend architecture. Uses SystemJS module format for dynamic loading.
   - **`"standalone"`**: For traditional single-page applications (SPA). Uses ES module format.
   - Internally maps to: `micro-frontend` → `vite_build_format: "system"`, `standalone` → `vite_build_format: "es"`

7. **test_framework** (enum)
   - Options: `"jest"` or `"vitest"`
   - Default: `"jest"`
   - Determines which testing framework to configure

8. **state_management** (enum)
   - Options: `"vuex"` or `"pinia"`
   - Default: `"vuex"`
   - Determines which state management library to use

### Optional Fields

14. **enable_single_spa** (boolean)
    - Default: `true`
    - Enable/disable single-spa micro frontend integration

15. **enable_datadog** (boolean)
    - Default: `true`
    - Enable/disable Datadog RUM monitoring

## Auto-Derived Values

The following values are automatically derived and don't need to be configured:

- **main_component_name**: Derived from `application_name` in PascalCase
  - Example: `omni-inventory-manager-web` → `OmniInventoryManagerWeb`

- **application_id**: Same as `application_name`, used for CSS isolation
  - Example: `omni-inventory-manager-web`

- **service_name**: Same as `application_name`, used for monitoring
  - Example: `omni-inventory-manager-web`

## Package Reference Selection

Based on `test_framework` and `state_management`, the agent will use the appropriate package.json reference:

| test_framework | state_management | Package Reference File |
|----------------|------------------|------------------------|
| `"jest"`       | `"vuex"`         | `jest-vuex.md`         |
| `"jest"`       | `"pinia"`        | `jest-pinia.md`        |
| `"vitest"`     | `"vuex"`         | `vitest-vuex.md`       |
| `"vitest"`     | `"pinia"`        | `vitest-pinia.md`      |

## Validation

The agent should:
1. Check if `config.json` exists in the project root
2. If it exists, validate it against `config.schema.json`
3. Use values from `config.json` for configured fields
4. Prompt for any missing required fields
5. Use default values for optional fields not provided

## Example Configurations

### Minimal Modern Stack
```json
{
  "application_name": "my-app-web",
  "project_scope": "@myorg",
  "router_base_path": "/my-app",
  "api_base_path": "/api",
  "default_port": 8080,
  "application_type": "standalone",
  "test_framework": "vitest",
  "state_management": "pinia"
}
```

### Legacy Stack with All Options
```json
{
  "application_name": "legacy-app-web",
  "project_scope": "@myorg",
  "router_base_path": "/legacy-app",
  "api_base_path": "/api/v1",
  "default_port": 3000,
  "application_type": "micro-frontend",
  "test_framework": "jest",
  "state_management": "vuex",
  "enable_single_spa": true,
  "enable_datadog": true,
}
```

### Standalone App (No Micro Frontend)
```json
{
  "application_name": "standalone-app",
  "project_scope": "@myorg",
  "router_base_path": "/",
  "api_base_path": "/api",
  "default_port": 3000,
  "application_type": "standalone",
  "test_framework": "vitest",
  "state_management": "pinia",
  "enable_single_spa": false,
  "enable_datadog": false,
}
```
