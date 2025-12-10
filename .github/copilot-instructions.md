# Instructions for GitHub Copilot Agents

This file contains application-specific parameters that should be used by agents when generating code and configuration files for Vue 3 Vite applications.

## Available Agents

### @app-starter
Agent specializing in bootstrapping and configuring Vue 3 Vite applications. Use this agent to generate the complete application structure including all configuration files, build setup, and project scaffolding.

**Usage**: `@app-starter generate a new Vue 3 application`

**Detailed Instructions**: See `agents/agents-context/app-starter/.copilot-instructions.md` for:
- Complete execution flow and orchestration logic
- Skills-based architecture overview
- Standalone vs micro-frontend comparison
- File generation workflow
- Validation checklists

**Skills Directory**: `agents/agents-context/app-starter/skills/` contains all implementation:
- `configuration/` - Config management (reads config.json, prompts, validates)
- `conditional-generation/` - Determines files to generate based on app type
- `component-library/` - Conditionally installs component library
- `package-json/` - Generates package.json with correct dependencies
- And 40+ other skills for complete application generation

## Application Parameters

Before generating code, the agent should ask the user for the following application-specific parameters:

### Required User Input

**IMPORTANT**: Ask these questions **ONE AT A TIME** in a conversational flow. Do NOT ask all questions at once.

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

7. **Do you want to use the latest package versions from npm?** (recommended for new projects)
   - Options: `yes` or `no`
   - This will be used as `use_latest_versions`
   - **`yes`**: Fetch latest versions from npm registry at generation time
     - Uses latest recommended test framework (Vitest)
     - Uses latest recommended state management (Pinia)
     - All dependencies fetched from npm at generation time
   - **`no`**: Use tested, stable versions and choose specific tools
     - Proceed to ask about test framework (question 8)
     - Proceed to ask about state management (question 9)
   - Used in: package.json generation to select version strategy

### Conditional Questions (Only if `use_latest_versions === false`)

8. **Do you want to use Vitest for testing?** (default: Jest)
   - **Only asked if user chose NO to latest versions**
   - Options: `vitest` or `jest`
   - This will be used as `test_framework`
   - If `vitest`: Use Vitest with @vitest/ui, configure vitest.config.ts
   - If `jest`: Use Jest with @vue/vue3-jest, configure jest.config.js

9. **Do you want to use Pinia for state management?** (default: Vuex)
   - **Only asked if user chose NO to latest versions**
   - Options: `pinia` or `vuex`
   - This will be used as `state_management`
   - If `pinia`: Use Pinia with TypeScript support, create stores in `src/stores/`
   - If `vuex`: Use Vuex 4 with modules, create store in `src/store/`

### Optional Questions (Always Asked)

10. **Do you want to include a component library?** (default: no)
    - Options: `yes` or `no`
    - This will be used as `include_component_library`
    - **`yes`**: Include @RoyalAholdDelhaize/pdl-spectrum-component-library-web
      - Proceeds to ask for GitHub token (question 11)
    - **`no`**: Skip component library and GitHub token
      - Skip question 11 entirely

11. **What is your GitHub Personal Access Token?** (for accessing @RoyalAholdDelhaize packages)
    - **Only asked if user chose YES to component library (question 10)**
    - This will be used as `github_token`
    - Used in: .npmrc for authenticating with GitHub Package Registry
    - Required permissions: `read:packages`
    - Token will be inserted into .npmrc to enable component library installation
    - **Security Note**: The token will be written to .npmrc which is gitignored. Never commit tokens to version control.

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
