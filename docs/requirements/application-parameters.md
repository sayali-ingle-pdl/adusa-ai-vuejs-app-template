## Application Parameters

Before generating code, the agent should ask the user for the following application-specific parameters:

### Question Flow Preference

**FIRST QUESTION - ALWAYS ASK THIS:**

**How would you like to provide the application configuration?**
- Options: `all-at-once` or `one-at-a-time`
- **`all-at-once`**: Display all questions together in a single list for the user to answer
- **`one-at-a-time`**: Interactive conversational flow where each question is asked individually after receiving the previous answer

Based on the user's choice:
- If **`all-at-once`**: Present questions 1-12 in a formatted list and wait for all answers
- If **`one-at-a-time`**: Ask questions 1-12 sequentially, waiting for each answer before proceeding

### Required User Input

Ask the user the following questions to gather the necessary parameters:

1. **What is the name of your application?** (in kebab-case, e.g., `omni-inventory-manager-web`)
   - This will be used as `application_name`
   - Used in: package.json name, vite.config.ts, README.md, index.html title

2. **What is your NPM scope/organization?** (e.g., `@pdl-fulfillment-omni`)
   - This will be used as `project_scope`
   - Used in: package.json name field
   - Format: `@{scope}/{application_name}`

3. **What base path should the router use?** (e.g., `/omni-inventory-manager`)
   - This will be used as `router_base_path`
   - Default format: `/` plus the application name without the `-web` suffix
   - Used in: Vue Router configuration

4. **What is the base API path for backend service proxying?** (e.g., `/omni-access-manager`, `/api`)
   - This will be used as `api_base_path`
   - Used in: Vite dev server proxy configuration, axios base URL

5. **What port should the development server use?** (e.g., `8089`)
   - This will be used as `default_port`
   - Used in: vite.config.ts server.port, README.md documentation, package.json scripts

6. **What type of application are you building?** 
   - Options: `micro-frontend` or `standalone`
   - **`micro-frontend`**: Your application will be part of a single-spa micro frontend architecture, where multiple applications can be composed together. This uses the SystemJS module format for dynamic loading and integration.
   - **`standalone`**: Your application will run independently as a traditional single-page application (SPA). This uses ES module format optimized for modern browsers.
   - This will be used as `application_type`
   - Internally maps to: `micro-frontend` → `vite_build_format: "system"`, `standalone` → `vite_build_format: "es"`
   - Used in: vite.config.ts build.lib.formats

### Optional Questions (Always Asked)

7. **Do you want to include a component library?** (default: no)
    - Options: `yes` or `no`
    - This will be used as `include_component_library`
    - **`yes`**: Include @RoyalAholdDelhaize/pdl-spectrum-component-library-web
      - Proceeds to ask for GitHub token (question 8)
    - **`no`**: Skip component library and GitHub token
      - Skip question 8 entirely

8. **What is your GitHub Personal Access Token?** (for accessing @RoyalAholdDelhaize packages)
    - **Only asked if user chose YES to component library (question 7)**
    - This will be used as `github_token`
    - Used in: .npmrc for authenticating with GitHub Package Registry
    - Required permissions: `read:packages`
    - Token will be inserted into .npmrc to enable component library installation
    - **Security Note**: The token will be written to .npmrc which is gitignored. Never commit tokens to version control.

9. **What Vue API pattern do you want to use?**
    - Options: `composition-api` or `options-api`
    - **`composition-api`**: Modern functional approach that organizes code by logical concerns using reactivity primitives like `ref()` and `reactive()`. Best for large applications requiring better code reusability and scalability through composables.
    - **`options-api`**: Traditional method that structures code into predefined options like `data`, `methods`, and `computed`. Familiar for developers coming from Vue 2.
    - This will be used as `vue_api_pattern`
    - Used in: Component template generation, view file structure
    - **Default recommendation**: `composition-api` for new Vue 3 projects

10. **What state management library do you want to use?**
    - Options: `pinia` or `vuex`
    - **`pinia`**: The recommended state management solution for Vue 3. Lightweight, intuitive API with built-in TypeScript support and great DevTools integration. Works seamlessly with Composition API.
    - **`vuex`**: The traditional Vue state management library. Still fully supported but considered legacy for new Vue 3 projects. Better suited for Options API or migrating from Vue 2.
    - This will be used as `state_management`
    - Used in: Store setup, store file generation, dependencies in package.json
    - **Default recommendation**: `pinia` for new Vue 3 projects

11. **What testing framework do you want to use?**
    - Options: `vitest` or `jest`
    - **`vitest`**: Modern testing framework built for Vite. Native ESM support, faster execution, better integration with Vite tooling, and includes UI mode for interactive testing.
    - **`jest`**: Mature and widely-used testing framework. Extensive ecosystem and documentation but requires additional configuration for ESM and Vite.
    - This will be used as `test_framework`
    - Used in: Test configuration files, test dependencies in package.json, test file templates
    - **Default recommendation**: `vitest` for new Vue 3 + Vite projects

12. **Do you want to use the latest package versions?**
    - Options: `yes` or `no`
    - **`yes`**: Automatically fetch and use the latest stable versions from npm registry for all packages (Vue, Vite, TypeScript, etc.)
    - **`no`**: Use pinned/tested versions specified in the template configuration
    - This will be used as `use_latest_versions`
    - Used in: package.json version resolution
    - **Default recommendation**: `yes` for new projects, `no` for production stability
    - **Note**: If `yes`, the generator will run `npm view <package> version` for each package and apply caret ranges

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
