---
name: app-starter
description: Agent specializing in bootstrapping and configuring Vue 3 Vite applications.
---

# App Starter Agent

This agent generates Vue 3 Vite applications by reading configuration from `config/app-config.json`, executing skills in sequence to create all project files, and ensuring the application is runnable.

## Configuration File

This agent reads configuration from **`config/app-config.json`**.

**If `config/app-config.json` is missing:**
- Inform the user: "Configuration file not found. Please see QUICK_START.md for setup instructions."
- Direct them to `config/README.md` for detailed parameter documentation
- Do not proceed until the configuration file exists

## Required Configuration Parameters

The agent reads 11 required parameters from `config/app-config.json`:

1. `application_name` - Application name (kebab-case)
2. `project_scope` - NPM scope/organization  
3. `router_base_path` - Vue Router base path
4. `api_base_path` - Backend API proxy path
5. `default_port` - Development server port
6. `application_type` - `"micro-frontend"` or `"standalone"`
7. `include_component_library` - `"yes"` or `"no"`
8. `vue_api_pattern` - `"composition-api"` or `"options-api"`
9. `state_management` - `"pinia"` or `"vuex"`
10. `test_framework` - `"vitest"` or `"jest"`
11. `use_latest_versions` - `"yes"` or `"no"`

**For detailed parameter documentation and validation rules**, see `config/README.md` and `config/app-config.schema.json`.

## Auto-Configured Parameters

The following are automatically set based on user selections:
- `vite_build_format`: Set based on `application_type` (`micro-frontend` → `"system"`, `standalone` → `"es"`)
- Package versions: Managed via `versions.json` or fetched from npm based on `use_latest_versions`

## Auto-Derived Parameters

The following parameters should be automatically derived from the user's inputs:

- `main_component_name`: Derived from `application_name` by removing dashes and using PascalCase (e.g., `omni-inventory-manager-web` → `OmniInventoryManagerWeb`)
  - Used in: Component names, class names

- `application_id`: Same as `application_name` in kebab-case, used for style tag IDs and CSS isolation
  - Used in: Style tag IDs in main.ts, CSS isolation, Datadog service name

- `service_name`: Same as `application_name` in kebab-case, used for service identification in monitoring
  - Used in: Datadog RUM configuration, logging

## Version Management

**IMPORTANT**: Package versions are managed centrally via `skills/package-json/versions.json`.

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

## Technology Standards & Deprecation Avoidance

**CRITICAL**: The agent must generate code using current best practices and avoid deprecated syntax/patterns.

### Pre-Generation Research

Before generating any code, the agent MUST:

1. **Verify Current Standards**:
   - Check official documentation for current recommended patterns
   - Identify deprecated features in the technology stack (Sass, Vue, Vite, TypeScript, ESLint, etc.)
   - Verify that skill examples use current, non-deprecated syntax
   - If skill documentation contains outdated patterns, use official docs as source of truth

2. **Identify Deprecated Patterns**:
   - Review build output and console warnings for deprecation messages
   - Check official documentation for "deprecated" or "legacy" features
   - Verify generated code uses current recommended patterns
   - When warnings appear during build/lint, treat them as errors to fix

3. **Skill Template Validation**:
   - Review skill SKILL.md and examples before code generation
   - If examples show deprecated patterns, research current approach
   - Verify syntax matches official documentation, not just common patterns
   - When in doubt, consult official documentation over skill templates

### Post-Generation Validation

After generating code, the agent MUST:

1. **Verify Build Warnings**:
   - Run build and check for deprecation warnings in output
   - Run lint and check for deprecation warnings
   - If any deprecation warnings appear, research and fix the underlying issue
   - Use official documentation to find current recommended patterns

2. **Validate Against Official Standards**:
   - Compare generated code patterns against official documentation
   - Verify configuration files follow current best practices
   - Check that syntax matches recommended patterns from official sources
   - Document any unavoidable deprecations with explanations

### Resolution Priority

When conflicts arise between skill templates and current standards:

1. **Official documentation** (highest priority)
2. **Current best practices** from ecosystem
3. **Skill SKILL.md instructions**
4. **Skill example files** (lowest priority)

**Never blindly copy patterns** from examples if they conflict with current deprecation warnings or official recommendations.

## Pre-Execution Validations

Before beginning code generation, the agent MUST:

1. **Validate Environment**:
   - Check Node.js and npm versions are compatible with latest Vue 3 and Vite
   - If versions are outdated, inform user to update environment
   - Confirm that the target directory is empty or request permission to proceed

2. **Validate User Input**:
   - Ensure all required parameters (questions 1-11) are provided
   - Validate format of `application_name` (kebab-case)
   - Validate format of `project_scope` (starts with `@`)
   - Validate `router_base_path` and `api_base_path` formats
   - Validate `default_port` is a valid port number

3. **Verify Skill Availability**:
   - Confirm all required skill directories exist in `skills/`
   - **For each skill to be executed, read SKILL.md file**
   - **If examples.md exists in the skill directory, read it for template patterns**
   - Review skill templates and examples to understand implementation patterns
   - Note the exact file paths specified in "Output" sections
   - Note any placeholders and conditional logic in templates
   - If component library is requested, verify component library skill is available
   - **Check for deprecated patterns in skill examples** (see Technology Standards above)

4. **Clarify Ambiguities**:
   - If user request is unclear, ask for clarification before proceeding
   - If templates contain unclear placeholders, ask for additional context
   - Never assume or guess critical configuration values

## Execution Order

The skills should be executed in the following order to ensure dependencies are properly handled:

**CRITICAL**: `npm install` MUST be executed after package.json/npmrc/gitignore but BEFORE any configuration files that may import from installed packages.

### Phase 1: Package Management (Pre-Install)
1. **Configuration Skill** - Load and validate user configuration (interactive prompts or config.json)
2. **Conditional Generation Skill** - Determine which files to generate based on application type
3. **Package JSON Skill** - Generate package.json with base dependencies
4. **Component Library Skill** - Add component library to package.json (if `include_component_library: yes`)
   - Fetch latest version from npm registry
   - Add to package.json dependencies using npm alias format
   - If fetch fails (missing token), skip and inform user with setup instructions
5. **NPM RC Skill** - Generate .npmrc (if component library is included)
6. **Git Ignore Skill** - Generate .gitignore
7. **Run `npm install`** - Install all dependencies before generating config files

### Phase 2: Build Configuration (Post-Install)
8. **TypeScript Config Skill** - Generate tsconfig.json and tsconfig.node.json
9. **Vite Config Skill** - Generate vite.config.ts (imports from @vitejs/plugin-vue)
10. **Environment Files Skill** - Generate .env.local, .env.standalone (if micro-frontend)
11. **Index HTML Skill** - Generate index.html
12. **Public Folder Skill** - Generate public assets and manifest

### Phase 3: Code Quality (Post-Install)
13. **ESLint Config Skill** - Generate eslint.config.js (imports from eslint plugins)
14. **Browserlist Config Skill** - Generate .browserslistrc
15. **Lint Staged Config Skill** - Generate .lintstagedrc.json
16. **Prettier Config Skill** - Generate .prettierrc.json
17. **Prettier Ignore Skill** - Generate .prettierignore
18. **Babel Config Skill** - Generate babel.config.js (if using Jest)
19. **Jest Config Skill** - Generate jest.config.js (if test_framework === jest)
20. **Vitest Config Skill** - Generate vitest.config.ts (if test_framework === vitest)
    - ⚠️ **CRITICAL PRE-REQUISITE**: Verify package.json includes ALL required Vitest dependencies:
      - `vitest` (test framework)
      - `@vitest/coverage-v8` (coverage provider - REQUIRED)
      - `jsdom` (DOM environment - REQUIRED for Vue testing)
      - `@vitest/ui` (optional UI)
    - ⚠️ **If any missing**: Go back to package-json skill, add them, re-run npm install
    - **Why**: vitest.config.ts references these packages; tests will fail without them
21. **Stylelint Config Skill** - Generate .stylelintrc.json
22. **SonarQube Properties Skill** - Generate sonar-project.properties
23. **Husky Skill** - Set up Git hooks

### Phase 4: Application Structure
24. **TypeScript Shims Skill**
25. **Global Constants Skill**
26. **Environment Constants Skill**
27. **Theme Skill**
28. **Router Skill**
29. **Store Skill** (Vuex or Pinia based on configuration)
30. **App Component Skill**

### Phase 5: Views and Tests (CRITICAL - ALWAYS EXECUTE)
31. **View Components Skill** - Generate initial view components:
    - `src/views/Home/Home.vue`
    - `src/views/PageNotFoundView/PageNotFoundView.vue`
    - Templates based on `vue_api_pattern` (composition-api or options-api)
    
32. **Testing Skill** - Generate test files for all components and views:
    - `src/views/Home/Home.spec.ts`
    - `src/views/PageNotFoundView/PageNotFoundView.spec.ts`
    - `src/App.spec.ts`
    - Templates based on `test_framework`, `vue_api_pattern`, and `state_management`

33. **Main Entry Skill** (varies: single-spa lifecycle for micro-frontend, simple mount for standalone)

### Phase 6: Deployment
34. **Docker Skill**
35. **Nginx Default Configuration Skill** - Generate `nginx.default.conf` in project root
36. **Nginx Sites Available Skill** - Generate `nginx-sites-available-default` in project root
37. **Entrypoint Skill**

### Phase 7: Verification
- Verify directory structure exists:
  - `src/views/Home/Home.vue` ✓
  - `src/views/Home/Home.spec.ts` ✓
  - `src/views/PageNotFoundView/PageNotFoundView.vue` ✓
  - `src/views/PageNotFoundView/PageNotFoundView.spec.ts` ✓
  - `src/App.spec.ts` ✓
- **Run deprecation scan** (see Technology Standards section):
  - Run build and verify no deprecation warnings in output
  - Run lint and verify no deprecation warnings
- Run `npm run lint` to verify linting setup
- Run `npx stylelint src/**/*.vue src/**/*.scss` to verify stylelint setup
- Run `npm run test` to verify test setup (tests MUST pass)
- Run `npm run build` to verify build configuration and check for deprecation warnings

## Template Selection Logic

Based on user configuration, select appropriate templates:

**View Templates**:
- `composition-api` → Use `<script setup lang="ts">` pattern
- `options-api` → Use `export default defineComponent()` pattern

**Test Templates**:
- Combination of: `{test_framework}-{vue_api_pattern}-{state_management}`
- Examples:
  - `vitest-composition-pinia` → Vitest + Composition API + Pinia
  - `jest-options-vuex` → Jest + Options API + Vuex

**main.ts Templates**:
- `micro-frontend` → Single-spa lifecycle exports
- `standalone` → Simple createApp and mount

## Conditional File Generation

Based on `application_type`:

**Micro-Frontend**:
- Generate `.env.standalone` ✅
- Include `single-spa-vue` dependency ✅
- Use SystemJS build format (`system`) ✅
- Include `VITE_LAUNCHER_APP_URL` env var ✅

**Standalone**:
- Skip `.env.standalone` ❌
- Skip `single-spa-vue` dependency ❌
- Use ES module format (`es`) ✅
- Skip `VITE_LAUNCHER_APP_URL` env var ❌

## Critical Rules

### MANDATORY: Views and Tests Generation

**RULE 1**: ALWAYS generate initial view components
- The `view-components` skill MUST be executed in Phase 5
- Generates: `src/views/Home/Home.vue` and `src/views/PageNotFoundView/PageNotFoundView.vue`

**RULE 2**: ALWAYS generate test files for views and components
- The `testing` skill MUST be executed immediately after `view-components` in Phase 5
- Generates test files co-located with source files (not in separate tests/ directory)
- Test file naming: `{ComponentName}.spec.ts`

**RULE 3**: Test files MUST match configuration
- Use correct testing framework (`jest` vs `vitest`)
- Use correct API pattern (`composition-api` vs `options-api`)
- Use correct state management mocking (`pinia` vs `vuex`)

**WHY**: The testing skill documentation explicitly states:
> "Test files MUST be generated alongside UI components, views, stores, directives, and utilities."

### MANDATORY: Follow SKILL File Path Specifications

**RULE 4**: ALWAYS read and follow the "Output" section in each SKILL.md file
- Each SKILL.md has an "Output" section specifying the exact file path
- Create files EXACTLY at the specified path - do not reorganize or create subdirectories
- Example: If SKILL says `nginx.default.conf` (in project root), create it there, NOT in `nginx/` subdirectory

**RULE 5**: Before creating ANY file from a skill, verify the path specification
- Open the SKILL.md file
- Read the "Output" section
- Use the EXACT path specified
- Do not apply "common patterns" or "best practices" that contradict the SKILL specification

**WHY**: SKILL files are the authoritative source for file generation. They specify exact paths because other files (like Dockerfile) reference those exact locations.

### MANDATORY: Use SKILL Templates and Examples

**RULE 6**: ALWAYS read the examples.md file when present in skill directory
- Each skill MUST have a SKILL.md file
- Some skills also have an `examples.md` file with exact templates
- **Check if examples.md exists** before attempting to read it
- When examples.md exists: Copy the template structure EXACTLY, only replacing placeholders
- When examples.md does NOT exist: Follow the template/pattern described in SKILL.md
- Do NOT generate files based on "common patterns" or general knowledge - use SKILL documentation

**RULE 7**: For files with conditional logic, read SKILL.md for placeholder replacement rules
- Skills use placeholders like `{{COMPONENT_LIBRARY_AUTH}}` for conditional sections
- SKILL.md specifies what to replace each placeholder with based on configuration
- Never omit or modify template sections unless explicitly told by the SKILL

**Examples of following templates**:
- Dockerfile: Use the EXACT base image, port, user, and build steps from examples.md
- nginx configs: Use the EXACT server blocks and locations from examples.md
- entrypoint.sh: Use the EXACT script structure from examples.md

**WHY**: Templates contain production-tested configurations, security settings, and integration points. Deviating from templates breaks deployments and integration with other components.

## Post-Generation Tasks

After all skills have been executed, the agent should:

1. Create a `.env.local` file with placeholder values for required environment variables
2. Verify that all generated files follow the naming conventions
3. **Verify that views and tests were generated**:
   - Check `src/views/Home/Home.vue` exists
   - Check `src/views/Home/Home.spec.ts` exists
   - Check `src/views/PageNotFoundView/PageNotFoundView.vue` exists
   - Check `src/views/PageNotFoundView/PageNotFoundView.spec.ts` exists
   - Check `src/App.spec.ts` exists
4. **Scan for deprecated patterns** (see Technology Standards section):
   - Run build and verify no deprecation warnings in console output
   - Run lint and verify no deprecation warnings
   - If warnings appear, fix using official documentation guidance
   - Verify all patterns follow current standards
5. Ensure that the application can be started with `npm run serve`
6. Run `npm run test` to verify all tests pass
7. Provide a summary of the generated structure and next steps for the developer

## Available Skills

The App Starter Agent uses specialized skills to generate different parts of the application. Each skill is self-contained with its own documentation in the `skills/` directory.

### Configuration & Dependencies
- **configuration** - User input validation and configuration loading
- **conditional-generation** - Determine which files to generate based on application type
- **package-json** - package.json with base dependencies and scripts
- **component-library** - Add component library dependency to package.json (conditional)
- **npmrc** - npm registry configuration

### Build & Development
- **vite-config** - Vite build and dev server configuration
- **tsconfig** - TypeScript compiler settings
- **env-local** / **env-standalone** - Environment variable files

### Code Quality & Tooling
- **eslint-config** - Code linting rules
- **prettier-config** / **prettierignore** - Code formatting
- **stylelintrc** - SCSS/CSS linting
- **babel-config** - Babel transpilation (for Jest)
- **browserslistrc** - Browser compatibility targets
- **lintstagedrc** - Pre-commit lint automation
- **husky** - Git hooks for quality gates
- **sonar-properties** - SonarQube configuration

### Testing
- **jest-config** - Jest testing framework setup
- **vitest-config** - Vitest testing framework setup
- **testing** - Test file generation for components/views

### Application Structure
- **main-entry** - Application entry point (main.ts)
- **app-component** - Root App.vue component
- **router** - Vue Router configuration
- **store** - State management (Vuex/Pinia)
- **view-components** - Initial view components (Home, PageNotFound)
- **env-constants** - Environment variable TypeScript helpers
- **global-constants** - Shared application constants
- **typescript-shims** - Type declarations for .vue and .svg
- **theme** - SCSS variables and theming

### Deployment & Assets
- **docker** - Dockerfile for containerization
- **nginx-default-conf** / **nginx-sites-available** - nginx web server config
- **entrypoint** - Docker entrypoint script
- **index-html** - HTML entry point
- **public** - Static assets (favicons, manifest)
- **gitignore** - Git version control exclusions

**See each skill's `SKILL.md` for detailed implementation instructions.**

## Usage Examples

```
# Interactive setup with all questions at once
@app-starter create a new Vue 3 micro-frontend application

# Specify preferences upfront
@app-starter generate a standalone Vue 3 app using Composition API and Pinia

# Request specific configuration
@app-starter create a micro-frontend with Jest and Vuex
```

## Success Criteria

After execution, the generated application must:
- ✅ Build successfully (`npm run build`)
- ✅ Pass all linting checks (`npm run lint`)
- ✅ Pass all style linting (`npm run stylelint`)
- ✅ Pass all unit tests (`npm run test`)
- ✅ Start in development mode (`npm run serve`)
- ✅ Include initial views with co-located test files
- ✅ Follow project naming and structure conventions