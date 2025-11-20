## Application Parameters

Before generating code, gather the following application-specific parameters from the user:

### Required User Input

1. **What is the name of your application?** (in kebab-case)
   - Parameter: `application_name`
   - Example: `omni-feedback-manager-web`
   - Used in: package.json name, vite.config.ts, README.md, index.html title

2. **What is your NPM scope/organization?**
   - Parameter: `project_scope`
   - Example: `@pdl-fulfillment-omni`
   - Used in: package.json name field (format: `@{scope}/{application_name}`)

3. **What base path should the router use?**
   - Parameter: `router_base_path`
   - Example: `/omni-feedback-manager`
   - Default: `/` plus the application name without the `-web` suffix
   - Used in: Vue Router configuration

4. **What is the base API path for backend service proxying?**
   - Parameter: `api_base_path`
   - Example: `/omni-access-manager` or `/api`
   - Used in: Vite dev server proxy configuration, axios base URL

5. **What port should the development server use?**
   - Parameter: `default_port`
   - Example: `8090`
   - Used in: vite.config.ts server.port, README.md documentation, package.json scripts

6. **What build format do you need?**
   - Parameter: `vite_build_format`
   - Options: `system` (for single-spa micro frontends) or `es` (for standalone apps)
   - Used in: vite.config.ts build.lib.formats

### Auto-Derived Parameters

The following parameters are automatically derived from user inputs:

- `main_component_name`: Derived from `application_name` by removing dashes and using PascalCase
  - Example: `omni-feedback-manager-web` → `OmniFeedbackManagerWeb`
  - Used in: Component names, class names

- `application_id`: Same as `application_name` in kebab-case
  - Example: `omni-feedback-manager-web`
  - Used in: Style tag IDs in main.ts, CSS isolation, Datadog service name

- `service_name`: Same as `application_name` in kebab-case
  - Example: `omni-feedback-manager-web`
  - Used in: Datadog RUM configuration, logging
