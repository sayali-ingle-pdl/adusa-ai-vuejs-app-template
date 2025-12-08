# Instructions for GitHub Copilot Agents

This file contains application-specific parameters that should be used by agents when generating code and configuration files for Vue 3 Vite applications.

## Available Agents

### @app-starter
Agent specializing in bootstrapping and configuring Vue 3 Vite applications. Use this agent to generate the complete application structure including all configuration files, build setup, and project scaffolding.

**Usage**: `@app-starter generate a new Vue 3 application`

## Application Parameters

Before generating code, the agent should ask the user for the following application-specific parameters:

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

7. **Do you want to use Vitest for testing?** (default: Jest)
   - Options: `vitest` or `jest`
   - This will be used as `test_framework`
   - If `vitest`: Use Vitest with @vitest/ui, configure vitest.config.ts
   - If `jest`: Use Jest with @vue/vue3-jest, configure jest.config.js

8. **Do you want to use Pinia for state management?** (default: Vuex)
   - Options: `pinia` or `vuex`
   - This will be used as `state_management`
   - If `pinia`: Use Pinia with TypeScript support, create stores in `src/stores/`
   - If `vuex`: Use Vuex 4 with modules, create store in `src/store/`

9. **What is your GitHub Personal Access Token?** (for accessing @RoyalAholdDelhaize packages)
   - This will be used as `github_token`
   - Used in: .npmrc for authenticating with GitHub Package Registry
   - Required permissions: `read:packages`
   - Token will be inserted into .npmrc to enable component library installation
   - **Security Note**: The token will be written to .npmrc which is gitignored. Never commit tokens to version control.

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

## Code Generation Guidelines

### Component Template

When generating Vue components, use this structure:

```vue
<template>
  <!-- Component template -->
</template>

<script setup lang="ts">
// Imports
import { ref, computed, onMounted } from 'vue';

// Props
interface Props {
  // prop definitions
}
const props = defineProps<Props>();

// Emits
const emit = defineEmits<{
  // emit definitions
}>();

// Component logic
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

// Scoped styles
</style>
```

### Service Template

When generating service files:

```typescript
import axios from 'axios';
import type { EntityType, SearchParams, ResponseType } from '@/interfaces/entity';

export const entityService = {
  async getAll(params: SearchParams): Promise<EntityType[]> {
    const response = await axios.get<ResponseType>('/api/entity', { params });
    return response.data.items;
  },
  
  async getById(id: string): Promise<EntityType> {
    const response = await axios.get<EntityType>(`/api/entity/${id}`);
    return response.data;
  }
};
```

### Store Module Template

#### Vuex Store Module (if using Vuex)

```typescript
import type { Module } from 'vuex';
import type { RootState } from '../index';

interface EntityState {
  // state properties
}

const entityModule: Module<EntityState, RootState> = {
  namespaced: true,
  
  state: () => ({
    // initial state
  }),
  
  mutations: {
    // mutations
  },
  
  actions: {
    // actions
  },
  
  getters: {
    // getters
  }
};

export default entityModule;
```

#### Pinia Store (if using Pinia)

```typescript
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';
import type { EntityType } from '@/interfaces/entity';

export const useEntityStore = defineStore('entity', () => {
  // State
  const items = ref<EntityType[]>([]);
  const loading = ref(false);
  
  // Getters
  const itemCount = computed(() => items.value.length);
  
  // Actions
  async function fetchItems() {
    loading.value = true;
    try {
      // fetch logic
    } finally {
      loading.value = false;
    }
  }
  
  return {
    items,
    loading,
    itemCount,
    fetchItems
  };
});
```

## Testing Conventions

### Jest Testing (if using Jest)
- Use Jest with Vue Test Utils for component testing
- Use `@vue/vue3-jest` transformer for `.vue` files
- Mock external dependencies (axios, router, store)
- Target >80% coverage for services and stores
- Target >70% coverage for components
- Test file naming: `{filename}.spec.ts`
- Configuration: `jest.config.cjs`

### Vitest Testing (if using Vitest)
- Use Vitest with Vue Test Utils for component testing
- Native ESM support with faster execution
- Use `@vitest/ui` for interactive test UI
- Mock external dependencies (axios, router, store)
- Target >80% coverage for services and stores
- Target >70% coverage for components
- Test file naming: `{filename}.spec.ts` or `{filename}.test.ts`
- Configuration: `vitest.config.ts`

## Linting and Formatting

- ESLint 9 with flat config format (`eslint.config.cjs`)
- Prettier 3 for code formatting
- Vue ESLint plugin for Vue-specific rules
- TypeScript ESLint for TypeScript rules
- Pre-commit hooks via Husky for automated checks
