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

When generating Vue components, use the structure based on the selected `vue_api_pattern`:

#### Composition API (Recommended)

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

// Component logic using ref(), reactive(), computed()
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

// Scoped styles
</style>
```

#### Options API

```vue
<template>
  <!-- Component template -->
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'ComponentName',
  
  props: {
    // prop definitions
  },
  
  emits: {
    // emit definitions
  },
  
  data() {
    return {
      // reactive data properties
    };
  },
  
  computed: {
    // computed properties
  },
  
  methods: {
    // component methods
  },
  
  mounted() {
    // lifecycle hooks
  }
});
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

**IMPORTANT**: Test files MUST be generated alongside UI components, views, stores, directives, and utilities.

For detailed testing guidelines, patterns, and templates, see the **testing skill**: `agents/agents-context/app-starter/skills/testing/`

### Quick Reference

- **Test file location**: Co-located with source files (not in separate `tests/` directory)
- **Naming convention**: `{filename}.spec.ts`
- **Coverage targets**: Components/Views >70%, Stores/Utilities >80%
- **Test patterns**: Component tests, View tests, Store tests (Vuex/Pinia), Utility tests, Directive tests

See `skills/testing/SKILL.md` for complete documentation and `skills/testing/examples.md` for test templates.

## Linting and Formatting

- ESLint 9 with flat config format (`eslint.config.cjs`)
- Prettier 3 for code formatting
- Vue ESLint plugin for Vue-specific rules
- TypeScript ESLint for TypeScript rules
- Pre-commit hooks via Husky for automated checks
