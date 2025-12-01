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
   - Build format
   - **GitHub Personal Access Token** (for @RoyalAholdDelhaize packages)
2. Read and understand additional parameters from `docs/requirements/application-parameters.md` if present
3. Execute all skills in sequence to generate the complete application structure
4. **Important**: When executing the NPM RC Skill, replace `{github_token}` with the actual token provided by the user
5. Run the required commands to install dependencies and verify the setup
6. Verify that the application is in a runnable state

## Execution Order

The skills should be executed in the following order to ensure dependencies are properly handled:

### Phase 1: Project Setup
1. Package JSON Skill
2. NPM RC Skill (**must use actual `github_token` value, not placeholder**)
3. Git Ignore Skill (ensure .npmrc is included)
4. **Run `npm install`** to install dependencies (will authenticate using token in .npmrc)
5. **Replace token in .npmrc** with environment variable placeholder:
   - Replace the actual `github_token` value with `${GH_PACKAGE_LIBRARY_TOKEN}`
   - This allows the .npmrc to be safely committed to version control
   - Final .npmrc should contain: `//npm.pkg.github.com/:_authToken=${GH_PACKAGE_LIBRARY_TOKEN}`

### Phase 2: Build Configuration
6. TypeScript Config Skill
7. Vite Config Skill
8. Environment Files Skill
9. Index HTML Skill
10. Public Folder Skill

### Phase 3: Code Quality
11. ESLint Config Skill
12. Prettier Config Skill
13. Babel Config Skill
14. Jest Config Skill
15. Husky Skill

### Phase 4: Application Structure
16. TypeScript Shims Skill
17. Global Constants Skill
18. Environment Constants Skill
19. Router Skill
20. Store Skill
21. App Component Skill
22. Main Entry Skill

### Phase 5: Deployment
23. Docker Skill

### Phase 6: Verification
- Run `npm run lint` to verify linting setup
- Run `npm run build` to verify build configuration
- Run `npm run test:unit` to verify test setup (if tests exist)

## Post-Generation Tasks

After all skills have been executed, the agent should:

1. Create a `.env.local` file with placeholder values for required environment variables
2. Verify that all generated files follow the naming conventions
3. Ensure that the application can be started with `npm run serve`
4. Provide a summary of the generated structure and next steps for the developer
