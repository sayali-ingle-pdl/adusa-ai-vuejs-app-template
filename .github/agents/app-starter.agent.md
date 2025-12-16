---
name: app-starter
description: Agent specializing in bootstrapping and configuring Vue 3 Vite applications.
---

# App Starter Agent

This agent is designed to assist in the initial setup and configuration of Vue 3 applications using Vite. It focuses on generating essential project files, setting up build configurations, and ensuring that the application adheres to best practices from the outset. The agent must leave the application in a runnable state after completing its tasks.

## Skills

The App Starter Agent has access to the following skills for generating project files and configurations. Each skill is organized in its own directory with SKILL.md as the main instruction file:

### Package Management Skills
1. **Package JSON Skill** (`./agents-context/app-starter/skills/package-json/`) - Generates package.json with dependencies and scripts
2. **NPM RC Skill** (`./agents-context/app-starter/skills/npmrc/`) - Generates .npmrc for package registry configuration

### Build and Development Skills
3. **Vite Config Skill** (`./agents-context/app-starter/skills/vite-config/`) - Generates Vite configuration for build and dev server
4. **TypeScript Config Skill** (`./agents-context/app-starter/skills/tsconfig/`) - Generates TypeScript compiler configuration
5. **Environment Files Skill** (`./agents-context/app-starter/skills/env-files/`) - Generates .env files for environment variables

### Code Quality Skills
6. **ESLint Config Skill** (`./agents-context/app-starter/skills/eslint-config/`) - Generates ESLint configuration for code linting
7. **Prettier Config Skill** (`./agents-context/app-starter/skills/prettier-config/`) - Generates Prettier configuration for code formatting
8. **Git Ignore Skill** (`./agents-context/app-starter/skills/gitignore/`) - Generates .gitignore file for version control

### Testing Skills
9. **Jest Config Skill** (`./agents-context/app-starter/skills/jest-config/`) - Generates Jest configuration for unit testing
10. **Babel Config Skill** (`./agents-context/app-starter/skills/babel-config/`) - Generates Babel configuration for test transpilation

### Application Structure Skills
11. **Main Entry Skill** (`./agents-context/app-starter/skills/main-entry/`) - Generates main.ts with single-spa lifecycle
12. **App Component Skill** (`./agents-context/app-starter/skills/app-component/`) - Generates root App.vue component
13. **Router Skill** (`./agents-context/app-starter/skills/router/`) - Generates Vue Router configuration
14. **Store Skill** (`./agents-context/app-starter/skills/store/`) - Generates Vuex store configuration
15. **Environment Constants Skill** (`./agents-context/app-starter/skills/env-constants/`) - Generates EnvConsts.ts for environment variables
16. **Global Constants Skill** (`./agents-context/app-starter/skills/global-constants/`) - Generates global constants file
17. **TypeScript Shims Skill** (`./agents-context/app-starter/skills/typescript-shims/`) - Generates TypeScript declaration files for Vue and SVG
18. **Theme Skill** (`./agents-context/app-starter/skills/theme/`) - Generates SCSS theme files and variables
19. **View Components Skill** (`./agents-context/app-starter/skills/view-components/`) - Generates initial view components

### Docker and Deployment Skills
20. **Docker Skill** (`./agents-context/app-starter/skills/docker/`) - Generates Dockerfile and nginx configuration
21. **Husky Skill** (`./agents-context/app-starter/skills/husky/`) - Generates Git hooks configuration

### HTML and Static Assets Skills
22. **Index HTML Skill** (`./agents-context/app-starter/skills/index-html/`) - Generates index.html entry point
23. **Public Folder Skill** (`./agents-context/app-starter/skills/public/`) - Generates public/ directory with favicons, app icons, and web manifest

## Usage

When invoked, this agent should:
1. Prompt the user for all required application parameters as defined in `copilot-instructions.md`:
   - Application name
   - NPM scope/organization
   - Router base path
   - API base path
   - Development server port
   - Application type (micro-frontend or standalone)
   - Vue API pattern (composition-api or options-api)
   - State management (pinia or vuex)
   - Testing framework (vitest or jest)
   - Use latest versions (yes or no)
   - Include component library (yes or no)
   - GitHub token (only if component library is yes)
2. Read and understand additional parameters from `docs/requirements/application-parameters.md` if present
3. Execute all skills in sequence to generate the complete application structure
4. **CRITICAL**: Execute `view-components` skill to generate initial views
5. **CRITICAL**: Execute `testing` skill to generate test files for all views and components
6. Run the required commands to install dependencies and verify the setup
7. Verify that the application is in a runnable state

## Execution Order

The skills should be executed in the following order to ensure dependencies are properly handled:

### Phase 1: Configuration and Setup
1. **Configuration Skill** - Load and validate user configuration (interactive prompts or config.json)
2. **Conditional Generation Skill** - Determine which files to generate based on application type
3. Package JSON Skill
4. NPM RC Skill (if component library is included)
5. Git Ignore Skill
6. **Run `npm install`** to install dependencies

### Phase 2: Build Configuration
7. TypeScript Config Skill
8. Vite Config Skill
9. Environment Files Skill (env-local, env-standalone if micro-frontend)
10. Index HTML Skill
11. Public Folder Skill

### Phase 3: Code Quality
12. ESLint Config Skill
13. Browserlist Config Skill
14. Lint Staged Config Skill
15. Prettier Config Skill
16. Prettier Ignore Skill
17. Babel Config Skill (if using Jest)
18. Jest Config Skill (if test_framework === jest)
19. Vitest Config Skill (if test_framework === vitest)
20. Stylelint Config Skill
21. SonarQube Properties Skill
22. Husky Skill

### Phase 4: Application Structure
23. TypeScript Shims Skill
24. Global Constants Skill
25. Environment Constants Skill
26. Theme Skill
27. Router Skill
28. Store Skill (Vuex or Pinia based on configuration)
29. App Component Skill

### Phase 5: Views and Tests (CRITICAL - ALWAYS EXECUTE)
30. **View Components Skill** - Generate initial view components:
    - `src/views/Home/Home.vue`
    - `src/views/PageNotFoundView/PageNotFoundView.vue`
    - Templates based on `vue_api_pattern` (composition-api or options-api)
    
31. **Testing Skill** - Generate test files for all components and views:
    - `src/views/Home/Home.spec.ts`
    - `src/views/PageNotFoundView/PageNotFoundView.spec.ts`
    - `src/App.spec.ts`
    - Templates based on `test_framework`, `vue_api_pattern`, and `state_management`

32. Main Entry Skill (varies: single-spa lifecycle for micro-frontend, simple mount for standalone)

### Phase 6: Deployment
33. Docker Skill
34. Nginx Default Configuration Skill
35. Nginx Sites Available Skill
36. Entrypoint Skill

### Phase 7: Component Library (Optional)
37. Component Library Skill (if include_component_library === true)

### Phase 8: Verification
- Verify directory structure exists:
  - `src/views/Home/Home.vue` ✓
  - `src/views/Home/Home.spec.ts` ✓
  - `src/views/PageNotFoundView/PageNotFoundView.vue` ✓
  - `src/views/PageNotFoundView/PageNotFoundView.spec.ts` ✓
  - `src/App.spec.ts` ✓
- Run `npm run lint` to verify linting setup
- Run `npx stylelint src/**/*.vue src/**/*.scss` to verify stylelint setup
- Run `npm run test` to verify test setup (tests MUST pass)
- Run `npm run build` to verify build configuration

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
4. Ensure that the application can be started with `npm run serve`
5. Run `npm run test` to verify all tests pass
6. Provide a summary of the generated structure and next steps for the developer

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

### Template Selection Logic

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

### Conditional File Generation

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
