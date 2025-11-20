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
9. **Editor Config Skill** (`./agents-context/app-starter/skills/editorconfig/`) - Generates .editorconfig for consistent editor settings

### Testing Skills
10. **Jest Config Skill** (`./agents-context/app-starter/skills/jest-config/`) - Generates Jest configuration for unit testing
11. **Babel Config Skill** (`./agents-context/app-starter/skills/babel-config/`) - Generates Babel configuration for test transpilation

### Application Structure Skills
12. **Main Entry Skill** (`./agents-context/app-starter/skills/main-entry/`) - Generates main.ts with single-spa lifecycle
13. **App Component Skill** (`./agents-context/app-starter/skills/app-component/`) - Generates root App.vue component
14. **Router Skill** (`./agents-context/app-starter/skills/router/`) - Generates Vue Router configuration
15. **Store Skill** (`./agents-context/app-starter/skills/store/`) - Generates Vuex store configuration
16. **Environment Constants Skill** (`./agents-context/app-starter/skills/env-constants/`) - Generates EnvConsts.ts for environment variables
17. **Global Constants Skill** (`./agents-context/app-starter/skills/global-constants/`) - Generates global constants file
18. **TypeScript Shims Skill** (`./agents-context/app-starter/skills/typescript-shims/`) - Generates TypeScript declaration files for Vue and SVG
19. **Theme Skill** (`./agents-context/app-starter/skills/theme/`) - Generates SCSS theme files and variables
20. **View Components Skill** (`./agents-context/app-starter/skills/view-components/`) - Generates initial view components

### Docker and Deployment Skills
21. **Docker Skill** (`./agents-context/app-starter/skills/docker/`) - Generates Dockerfile and nginx configuration
22. **Husky Skill** (`./agents-context/app-starter/skills/husky/`) - Generates Git hooks configuration

### HTML Skill
23. **Index HTML Skill** (`./agents-context/app-starter/skills/index-html/`) - Generates index.html entry point

## Usage

When invoked, this agent should:
1. Read and understand the application parameters from the `copilot-instructions.md` file
2. Read and understand the application parameters from the `docs/requirements/application-parameters.md` file
3. Execute all skills in sequence to generate the complete application structure
4. Generate all necessary files according to the skill specifications
5. Run the required commands to install dependencies and verify the setup
6. Verify that the application is in a runnable state

## Execution Order

The skills should be executed in the following order to ensure dependencies are properly handled:

### Phase 1: Project Setup
1. Package JSON Skill
2. NPM RC Skill
3. Git Ignore Skill
4. Editor Config Skill

### Phase 2: Build Configuration
5. TypeScript Config Skill
6. Vite Config Skill
7. Environment Files Skill
8. Index HTML Skill

### Phase 3: Code Quality
9. ESLint Config Skill
10. Prettier Config Skill
11. Babel Config Skill
12. Jest Config Skill
13. Husky Skill

### Phase 4: Application Structure
14. TypeScript Shims Skill
15. Global Constants Skill
16. Environment Constants Skill
17. Router Skill
18. Store Skill
19. App Component Skill
20. Main Entry Skill

### Phase 5: Deployment
21. Docker Skill

### Phase 6: Verification
- Run `npm install` to install dependencies
- Run `npm run lint` to verify linting setup
- Run `npm run build` to verify build configuration
- Run `npm run test:unit` to verify test setup (if tests exist)

## Post-Generation Tasks

After all skills have been executed, the agent should:

1. Create a `.env.local` file with placeholder values for required environment variables
2. Verify that all generated files follow the naming conventions
3. Ensure that the application can be started with `npm run serve`
4. Provide a summary of the generated structure and next steps for the developer
