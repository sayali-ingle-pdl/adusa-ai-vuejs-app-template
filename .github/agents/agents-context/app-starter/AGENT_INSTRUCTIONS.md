# Configuration Usage Instructions for Agent

## Overview

The agent should support two methods for gathering application configuration:
1. **Interactive prompts** - Ask user questions in terminal
2. **Configuration file** - Read from `config.json` in project root

## Configuration Loading Logic

### Step 1: Check for config.json

```javascript
// Pseudocode for agent logic
const configPath = path.join(projectRoot, 'config.json');
let userConfig = {};
let missingFields = [];

if (fs.existsSync(configPath)) {
  // Load and parse config.json
  userConfig = JSON.parse(fs.readFileSync(configPath, 'utf8'));
  
  // Validate against schema (optional but recommended)
  // validateAgainstSchema(userConfig, config.schema.json);
}
```

### Step 2: Identify Missing Required Fields

```javascript
const requiredFields = [
  'application_name',
  'project_scope',
  'router_base_path',
  'api_base_path',
  'default_port',
  'application_type',
  'test_framework',
  'state_management'
];

missingFields = requiredFields.filter(field => !userConfig[field]);
```

### Step 3: Prompt for Missing Fields Only

```javascript
// Only prompt for fields not in config.json
for (const field of missingFields) {
  userConfig[field] = await promptUser(field);
}
```

### Step 4: Apply Defaults for Optional Fields

```javascript
const defaults = {
  node_version: 'v22.16.0',
  vue_version: '^3.5.13',
  vite_version: '^6.3.5',
  typescript_version: '^5.7.3',
  enable_single_spa: true,
  enable_datadog: true,
  component_library: '@royalaholddelhaize/pdl-spectrum-component-library-web',
  component_library_version: '^1.0.3'
};

for (const [key, defaultValue] of Object.entries(defaults)) {
  if (!(key in userConfig)) {
    userConfig[key] = defaultValue;
  }
}
```

### Step 4.5: Validate Component Library Requirements

```javascript
// Component library requires BOTH library name AND github token
// If github_token is missing, skip component library installation
if (!userConfig.github_token || userConfig.github_token.trim() === '') {
  if (userConfig.component_library && userConfig.component_library !== '') {
    console.log('⚠️  GitHub token not provided. Skipping component library installation.');
    console.log('   To include component library, provide github_token in config.json or when prompted.\n');
  }
  
  // Clear component library settings when no token
  userConfig.component_library = '';
  userConfig.component_library_version = '';
}

// Set flag for package.json generation
userConfig.include_component_library = Boolean(
  userConfig.component_library && 
  userConfig.component_library.trim() !== '' &&
  userConfig.github_token &&
  userConfig.github_token.trim() !== ''
);
```

### Step 5: Derive Computed Values

```javascript
// Auto-derive values from user inputs
userConfig.main_component_name = toPascalCase(
  userConfig.application_name.replace(/-/g, '')
);
userConfig.application_id = userConfig.application_name;
userConfig.service_name = userConfig.application_name;

// Map application_type to vite_build_format
userConfig.vite_build_format = userConfig.application_type === 'micro-frontend' ? 'system' : 'es';

// Set conditional flags based on application_type
userConfig.is_microfrontend = userConfig.application_type === 'micro-frontend';
userConfig.is_standalone = userConfig.application_type === 'standalone';

// Validate consistency
if (userConfig.is_standalone && userConfig.enable_single_spa) {
  console.warn('⚠️  Warning: Standalone apps typically do not use single-spa.');
  console.warn('   Setting enable_single_spa to false.\n');
  userConfig.enable_single_spa = false;
}
```

### Step 6: Determine Conditional File Generation

```javascript
// Files to skip for standalone applications
const filesToGenerate = {
  '.env.standalone': userConfig.is_microfrontend,  // Only for micro-frontends
  'src/main.ts': {
    template: userConfig.is_microfrontend ? 'main-microfrontend.ts' : 'main-standalone.ts'
  }
};

// Dependencies to include/exclude
const conditionalDependencies = {
  'single-spa-vue': userConfig.is_microfrontend,  // Only for micro-frontends
};

// Environment variables to include/exclude in EnvConsts.ts
const conditionalEnvVars = {
  'VITE_LAUNCHER_APP_URL': userConfig.is_microfrontend,  // Only for micro-frontends
};

// index.html mount point
const mountPoint = userConfig.is_microfrontend 
  ? `#single-spa-application:${userConfig.application_name}`
  : '#app';
```

### Step 7: Select Package Reference

```javascript
const packageReferenceMap = {
  'jest-vuex': 'jest-vuex.md',
  'jest-pinia': 'jest-pinia.md',
  'vitest-vuex': 'vitest-vuex.md',
  'vitest-pinia': 'vitest-pinia.md'
};

const referenceKey = `${userConfig.test_framework}-${userConfig.state_management}`;
const packageReference = packageReferenceMap[referenceKey];

// Load the appropriate package reference file
const referenceContent = fs.readFileSync(
  `skills/package-json/reference/${packageReference}`,
  'utf8'
);
```

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

### github_token (optional)
- **MinLength**: 40
- **Example**: `ghp_1234567890abcdefghijklmnopqrstuvwxyz1234`
- **Note**: If not provided in config and component library is used, prompt user
- **Security**: Never log or display token

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

9. **Question 9**: "What is your GitHub Personal Access Token? (for accessing @RoyalAholdDelhaize packages, press Enter to skip)"

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

### Example 4: Config with optional fields
```json
{
  "application_name": "my-app-web",
  "project_scope": "@myorg",
  "router_base_path": "/my-app",
  "api_base_path": "/api",
  "default_port": 8080,
  "application_type": "standalone",
  "test_framework": "vitest",
  "state_management": "pinia",
  "enable_single_spa": false,
  "component_library": ""
}
```
**Agent behavior**: 
- Use all values from config
- `application_type: "standalone"` → Internally sets `vite_build_format: "es"`
- `enable_single_spa: false` → Don't include single-spa dependencies
- `component_library: ""` → Don't include component library

### Example 5: Config with component library but no GitHub token
```json
{
  "application_name": "my-app-web",
  "project_scope": "@myorg",
  "router_base_path": "/my-app",
  "api_base_path": "/api",
  "default_port": 8080,
  "application_type": "micro-frontend",
  "test_framework": "vitest",
  "state_management": "pinia",
  "component_library": "@myorg/components",
  "github_token": ""
}
```
**Agent behavior**:
- Warning: "⚠️  GitHub token not provided. Skipping component library installation."
- Component library is NOT added to package.json
- `.npmrc` file is NOT created

### Example 6: Config with both component library and GitHub token
```json
{
  "application_name": "my-app-web",
  "project_scope": "@myorg",
  "router_base_path": "/my-app",
  "api_base_path": "/api",
  "default_port": 8080,
  "application_type": "micro-frontend",
  "test_framework": "vitest",
  "state_management": "pinia",
  "component_library": "@myorg/components",
  "component_library_version": "^2.0.0",
  "github_token": "ghp_1234567890abcdef"
}
```
**Agent behavior**:
- Component library IS added to package.json dependencies
- `.npmrc` file IS created with github_token
- Success message shown
- Internally sets `vite_build_format: "system"`

## Output to User

When config.json is found and loaded:
```
✓ Configuration loaded from config.json
  - application_name: my-app-web
  - test_framework: vitest
  - state_management: pinia
  
Missing required fields:
  ? What is your NPM scope/organization? (e.g., '@pdl-fulfillment-omni')
```

When config.json is not found:
```
No config.json found. Please answer the following questions:
  
  ? What is the name of your application? (in kebab-case)
```

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

## Integration with Package References

After loading configuration, select the package reference:

```javascript
const referenceSelection = {
  test_framework: userConfig.test_framework,  // 'jest' or 'vitest'
  state_management: userConfig.state_management  // 'vuex' or 'pinia'
};

// Map to reference file
const referenceFile = `${referenceSelection.test_framework}-${referenceSelection.state_management}.md`;
// Results in: jest-vuex.md, jest-pinia.md, vitest-vuex.md, or vitest-pinia.md

// Load package.json template from selected reference
const packageJsonReference = loadReference(`skills/package-json/reference/${referenceFile}`);
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
