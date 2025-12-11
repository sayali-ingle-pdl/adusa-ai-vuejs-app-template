# Configuration Usage Instructions for Agent

## Overview

The agent should support two methods for gathering application configuration:
1. **Interactive prompts** - Ask user questions in terminal
2. **Configuration file** - Read from `config.json` in project root

## Field Validation Rules

### application_name
- **Pattern**: `^[a-z0-9]+(-[a-z0-9]+)*$` (kebab-case)
- **Example**: `omni-inventory-manager-web`
- **Error**: "Application name must be in kebab-case (lowercase with hyphens)"

### project_scope
- **Pattern**: `^@[a-z0-9-]+$` (npm scope format)
- **Example**: `@pdl-fulfillment-omni`
- **Error**: "Project scope must start with @ and contain only lowercase letters, numbers, and hyphens"

### router_base_path
- **Pattern**: `^/[a-z0-9-/]*$` (path format)
- **Example**: `/omni-inventory-manager`
- **Error**: "Router base path must start with / and contain only lowercase letters, numbers, hyphens, and slashes"

### api_base_path
- **Pattern**: `^/[a-z0-9-/]*$` (path format)
- **Example**: `/omni-access-manager` or `/api`
- **Error**: "API base path must start with / and contain only lowercase letters, numbers, hyphens, and slashes"

### default_port
- **Type**: integer
- **Range**: 1024-65535
- **Example**: 8089
- **Error**: "Port must be between 1024 and 65535"

### application_type
- **Enum**: `["micro-frontend", "standalone"]`
- **Example**: `micro-frontend`
- **Error**: "Application type must be either 'micro-frontend' or 'standalone'"
- **Note**: Internally maps to vite_build_format: `micro-frontend` → `system`, `standalone` → `es`

### test_framework
- **Enum**: `["jest", "vitest"]`
- **Example**: `vitest`
- **Error**: "Test framework must be either 'jest' or 'vitest'"

### state_management
- **Enum**: `["vuex", "pinia"]`
- **Example**: `pinia`
- **Error**: "State management must be either 'vuex' or 'pinia'"

## Prompt Messages (When config.json is not used)

Use these exact prompts when asking users interactively:

1. **Question 1**: "What is the name of your application? (in kebab-case, e.g., 'omni-inventory-manager-web')"

2. **Question 2**: "What is your NPM scope/organization? (e.g., '@pdl-fulfillment-omni')"

3. **Question 3**: "What base path should the router use? (e.g., '/omni-inventory-manager')"

4. **Question 4**: "What is the base API path for backend service proxying? (e.g., '/omni-access-manager', '/api')"

5. **Question 5**: "What port should the development server use? (e.g., 8089)"

6. **Question 6**: "What type of application are you building? (Options: 'micro-frontend' for single-spa architecture, 'standalone' for traditional SPA)"

7. **Question 7**: "Do you want to use Vitest for testing? (Options: 'vitest' or 'jest', default: 'jest')"

8. **Question 8**: "Do you want to use Pinia for state management? (Options: 'pinia' or 'vuex', default: 'vuex')"

## Usage Examples

### Example 1: All values in config.json
```json
// config.json exists with all required fields
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
**Agent behavior**: Read all values from config, skip all prompts, proceed with generation. Internally sets `vite_build_format: "system"`.

### Example 2: Partial config.json
```json
// config.json exists with some fields
{
  "application_name": "my-app-web",
  "test_framework": "vitest",
  "state_management": "pinia"
}
```
**Agent behavior**: 
- Read `application_name`, `test_framework`, `state_management` from config
- Prompt user for: `project_scope`, `router_base_path`, `api_base_path`, `default_port`, `application_type`

### Example 3: No config.json
**Agent behavior**: Prompt user for all 8 required questions.

## Error Handling

### Invalid JSON
```
✗ Error: config.json contains invalid JSON
  Please fix the syntax errors and try again.
```

### Schema Validation Failed
```
✗ Error: config.json validation failed
  - application_name: must match pattern ^[a-z0-9]+(-[a-z0-9]+)*$
  - default_port: must be between 1024 and 65535
```

### Missing Required Fields (with no prompting enabled)
```
✗ Error: config.json is missing required fields
  Missing: project_scope, router_base_path, api_base_path
  
  Please add these fields to config.json or run in interactive mode.
```

## Summary

The agent should:
1. ✅ Check for `config.json` in project root
2. ✅ Load and validate config if it exists
3. ✅ Prompt only for missing required fields
4. ✅ Apply defaults for optional fields
5. ✅ Derive computed values (main_component_name, etc.)
6. ✅ Select correct package reference based on test_framework + state_management
7. ✅ Generate application using combined configuration
8. ✅ Handle errors gracefully with clear messages
