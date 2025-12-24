# Instructions for GitHub Copilot

This file contains general instructions and conventions for all GitHub Copilot agents working in Vue 3 Vite application projects.

## Available Agents

### @app-starter
Agent specializing in bootstrapping and configuring Vue 3 Vite applications. Use this agent to generate the complete application structure including all configuration files, build setup, and project scaffolding.

**Usage**: `@app-starter generate a new Vue 3 application`

**Detailed Instructions**: See `agents/agents-context/app-starter/.copilot-instructions.md` for:
- Complete user questionnaire and configuration flow
- Execution phases and orchestration logic
- Skills-based architecture overview
- Template selection logic
- Validation checklists

**Skills Directory**: `agents/agents-context/app-starter/skills/` contains all implementation details for generating specific files and configurations.

## File Naming Conventions

- **Vue Components**: PascalCase (e.g., `InventoryList.vue`, `SearchBar.vue`)
- **TypeScript Files**: camelCase (e.g., `inventoryService.ts`, `authGuard.ts`)
- **Directories**: kebab-case (e.g., `inventory-list/`, `common-components/`)
- **Test Files**: Same as source file with `.spec.ts` extension (e.g., `inventoryService.spec.ts`)
- **Interface Files**: PascalCase, typically matching the entity name (e.g., `InventoryItem.ts`, `User.ts`)

## Project Structure Conventions

```
public/                     # Static assets served directly
├── favicon.ico
├── favicon-16x16.png
├── favicon-32x32.png
├── apple-touch-icon.png
├── android-chrome-192x192.png
├── android-chrome-512x512.png
└── site.webmanifest
src/
├── App.vue                 # Root component
├── main.ts                 # Application entry point and single-spa lifecycle
├── assets/                 # Static assets (images, fonts)
├── components/             # Reusable Vue components
│   ├── common/            # Shared components
│   └── {feature}/         # Feature-specific components
├── views/                  # Page-level components (routes)
│   └── {ViewName}/        # View directory with view component and related files
├── router/                 # Vue Router configuration
│   └── index.ts
├── store/                  # State management (Vuex or Pinia)
│   ├── index.ts           # If using Vuex: store setup
│   └── modules/           # If using Vuex: store modules by feature
├── stores/                 # If using Pinia: Pinia stores by feature
│   └── {entity}Store.ts
├── services/               # API service layer
│   └── {entity}Service.ts
├── interfaces/             # TypeScript interfaces and types
│   └── {entity}.ts
├── directives/             # Custom Vue directives
├── shared/                 # Shared utilities and constants
│   ├── constants/
│   ├── utils/
│   └── EnvConsts.ts       # Environment variable exports
└── theme/                  # SCSS theme files
    ├── vars.scss
    ├── shadow.scss
    ├── _mixins.scss
    └── index.scss
```