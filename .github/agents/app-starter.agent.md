---
name: app-starter
description: Agent specializing in bootstrapping and configuring Vue 3 Vite applications.
---

# App Starter Agent

This agent is designed to assist in the initial setup and configuration of Vue 3 applications using Vite. It focuses on generating essential project files, setting up build configurations, and ensuring that the application adheres to best practices from the outset. The agent must leave the application in a runnable state after completing its tasks.

## How This Agent Works

When invoked, this agent will:
1. **Gather Requirements** - Prompt for application configuration (name, type, preferences)
2. **Generate Project Structure** - Execute skills to create all necessary files and directories
3. **Install Dependencies** - Run npm install and verify the setup
4. **Verify & Test** - Ensure the application builds, lints, and tests pass

## Detailed Instructions

**For complete implementation details, see:**  
[`./app-starter/agents-context/.copilot-instructions.md`](./app-starter/agents-context/.copilot-instructions.md)

The detailed instructions include:
- Complete user questionnaire (12 configuration questions)
- Question flow strategies (all-at-once vs one-at-a-time)
- Version management and dependency resolution
- 8-phase execution order with skill dependencies
- Template selection logic for different configurations
- Conditional file generation rules
- Validation and verification procedures

## Available Skills

The App Starter Agent uses specialized skills to generate different parts of the application. Each skill is self-contained with its own documentation in the `skills/` directory.

### Configuration & Dependencies
- **package-json** - package.json with dependencies and scripts
- **npmrc** - npm registry configuration
- **configuration** - User input validation and configuration loading

### Build & Development
- **vite-config** - Vite build and dev server configuration
- **tsconfig** - TypeScript compiler settings
- **env-local/env-standalone** - Environment variable files

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
- **component-library** - Optional component library integration

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

## Key Configuration Questions

The agent will ask the following to determine project setup:

1. **Application name** - kebab-case (e.g., `omni-inventory-manager-web`)
2. **NPM scope** - organization (e.g., `@pdl-fulfillment-omni`)
3. **Router base path** - URL prefix (e.g., `/omni-inventory-manager`)
4. **API base path** - Backend proxy path (e.g., `/api`)
5. **Dev server port** - Port number (e.g., `8089`)
6. **Application type** - `micro-frontend` or `standalone`
7. **Vue API pattern** - `composition-api` or `options-api`
8. **State management** - `pinia` or `vuex`
9. **Testing framework** - `vitest` or `jest`
10. **Use latest versions** - Auto-fetch latest npm packages
11. **Include component library** - Add shared component library

**For detailed question descriptions and derivations, see the detailed instructions file.**

## Success Criteria

After execution, the generated application must:
- ✅ Build successfully (`npm run build`)
- ✅ Pass all linting checks (`npm run lint`)
- ✅ Pass all style linting (`npm run stylelint`)
- ✅ Pass all unit tests (`npm run test`)
- ✅ Start in development mode (`npm run serve`)
- ✅ Include initial views with co-located test files
- ✅ Follow project naming and structure conventions