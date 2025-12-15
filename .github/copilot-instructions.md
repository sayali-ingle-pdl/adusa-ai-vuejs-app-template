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

### Test File Generation Rules

When generating any of the following, **ALWAYS create a corresponding test file**:
- **Vue Components** (`src/components/**/*.vue`) → `{ComponentName}.spec.ts` in same directory
- **Views** (`src/views/**/*.vue`) → `{ViewName}.spec.ts` in same directory
- **Store Modules** (Vuex: `src/store/modules/*.ts`, Pinia: `src/stores/*.ts`) → `{moduleName}.spec.ts` in same directory
- **Directives** (`src/directives/*.ts`) → `{directiveName}.spec.ts` in same directory
- **Utilities** (`src/shared/utils/*.ts`) → `{utilityName}.spec.ts` in same directory
- **Mixins** (`src/shared/mixins/*.ts`) → `{mixinName}.spec.ts` in same directory
- **Services** (`src/services/*.ts`) → **OPTIONAL** - typically mocked in component tests

### Test File Location
- Test files live **co-located** with their source files (not in separate `tests/` directory)
- Naming convention: `{filename}.spec.ts` (use `.spec.ts` consistently)

### Jest Testing (if using Jest)
- Use Jest with Vue Test Utils (`@vue/test-utils`) for component testing
- Use `@vue/vue3-jest` transformer for `.vue` files
- Use `ts-jest` for TypeScript files
- Mock external dependencies (axios, router, store)
- Target >80% coverage for services and stores
- Target >70% coverage for components
- Test file naming: `{filename}.spec.ts`
- Configuration: `jest.config.cjs`

#### Jest Configuration Template
```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  collectCoverage: true,
  collectCoverageFrom: [
    '**/src/**/*.{ts,js,vue}',
    '!**/tests/**',
    '!**/node_modules/**',
    '!**/src/**/interfaces/*.ts',
    '!**/src/**/router/*.ts',
    '!**/src/**/services/**/*.ts',
    '!**/src/**/shared/constants/**/*.ts',
    '!**/src/**/shared/EnvConsts.ts',
    '!**/src/store/index.ts',
    '!**/src/App.vue',
    '!**/src/main.ts',
  ],
  testMatch: ['**/src/**/*.spec.{ts,js,vue}'],
  moduleFileExtensions: ['ts', 'js', 'json', 'vue'],
  transform: {
    '^.+\\.vue$': '@vue/vue3-jest',
    '^.+\\.tsx?$': 'ts-jest',
    '^.+\\.js$': 'babel-jest',
  },
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '\\.svg$': '<rootDir>/tests/unit/svgMock.cjs',
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
};
```

### Vitest Testing (if using Vitest)
- Use Vitest with Vue Test Utils for component testing
- Native ESM support with faster execution
- Use `@vitest/ui` for interactive test UI
- Mock external dependencies (axios, router, store)
- Target >80% coverage for services and stores
- Target >70% coverage for components
- Test file naming: `{filename}.spec.ts` or `{filename}.test.ts`
- Configuration: `vitest.config.ts`

### Component Test Template (Jest + Options API)

When generating tests for components using **Options API**:

```typescript
import { shallowMount } from '@vue/test-utils';
import { createStore } from 'vuex';
import ComponentName from './ComponentName.vue';

describe('ComponentName.vue', () => {
  // Mock store modules if component uses Vuex
  const mockStoreModule = {
    getters: {
      getSomeData: jest.fn().mockImplementation(() => 'mock data'),
    },
    actions: {
      fetchData: jest.fn(),
    },
    namespaced: true,
  };

  const createWrapper = (customOptions = {}) => {
    const store = createStore({
      modules: {
        moduleName: mockStoreModule,
      },
    });

    return shallowMount(ComponentName, {
      global: {
        plugins: [store],
      },
      ...customOptions,
    });
  };

  afterEach(() => {
    jest.clearAllMocks();
  });

  it('component exists', () => {
    const wrapper = createWrapper();
    expect(wrapper.exists()).toBe(true);
  });

  describe('mounted lifecycle', () => {
    it('fetches data on mount', () => {
      createWrapper();
      expect(mockStoreModule.actions.fetchData).toHaveBeenCalled();
    });
  });

  describe('computed properties', () => {
    it('returns correct computed value', () => {
      const wrapper = createWrapper();
      expect(wrapper.vm.computedProperty).toBe('expected value');
    });
  });

  describe('methods', () => {
    it('handles button click correctly', async () => {
      const wrapper = createWrapper();
      await wrapper.find('button').trigger('click');
      expect(mockStoreModule.actions.fetchData).toHaveBeenCalled();
    });
  });
});
```

### Component Test Template (Jest + Composition API)

When generating tests for components using **Composition API**:

```typescript
import { shallowMount } from '@vue/test-utils';
import { createPinia, setActivePinia } from 'pinia';
import ComponentName from './ComponentName.vue';
import { useEntityStore } from '@/stores/entityStore';

describe('ComponentName.vue', () => {
  beforeEach(() => {
    setActivePinia(createPinia());
  });

  const createWrapper = (props = {}) => {
    return shallowMount(ComponentName, {
      global: {
        plugins: [createPinia()],
      },
      props,
    });
  };

  afterEach(() => {
    jest.clearAllMocks();
  });

  it('component exists', () => {
    const wrapper = createWrapper();
    expect(wrapper.exists()).toBe(true);
  });

  describe('props', () => {
    it('receives and displays prop correctly', () => {
      const wrapper = createWrapper({ title: 'Test Title' });
      expect(wrapper.text()).toContain('Test Title');
    });
  });

  describe('emits', () => {
    it('emits event on button click', async () => {
      const wrapper = createWrapper();
      await wrapper.find('button').trigger('click');
      expect(wrapper.emitted('update')).toBeTruthy();
    });
  });

  describe('composables/stores', () => {
    it('calls store action', async () => {
      const wrapper = createWrapper();
      const store = useEntityStore();
      const fetchSpy = jest.spyOn(store, 'fetchItems');
      
      await wrapper.vm.loadData();
      expect(fetchSpy).toHaveBeenCalled();
    });
  });
});
```

### View Test Template

Views typically test:
- Lifecycle hooks (mounted, beforeUnmount)
- Navigation and routing
- Store interactions
- Event handlers
- Conditional rendering

```typescript
import { shallowMount } from '@vue/test-utils';
import { createStore } from 'vuex';
import ViewName from './ViewName.vue';

describe('ViewName.vue', () => {
  const createWrapper = () => {
    const store = createStore({
      modules: {
        // mock store modules
      },
    });

    return shallowMount(ViewName, {
      global: {
        plugins: [store],
        stubs: ['router-link'],
      },
    });
  };

  it('view exists', () => {
    const wrapper = createWrapper();
    expect(wrapper.exists()).toBe(true);
  });

  describe('mounted lifecycle', () => {
    it('sets up event listeners on mount', () => {
      const addEventListenerSpy = jest.spyOn(document, 'addEventListener');
      const wrapper = createWrapper();
      expect(addEventListenerSpy).toHaveBeenCalledWith(
        'click',
        expect.any(Function),
        true
      );
      wrapper.unmount();
    });
  });

  describe('navigation', () => {
    it('navigates to correct route on action', async () => {
      const wrapper = createWrapper();
      const pushMock = jest.fn();
      Object.defineProperty(wrapper.vm, '$router', {
        value: { push: pushMock },
      });
      
      await wrapper.vm.navigateToDetails('123');
      expect(pushMock).toHaveBeenCalledWith({
        name: 'Details',
        params: { id: '123' },
      });
    });
  });
});
```

### Store Test Template (Vuex)

```typescript
import store from '@/store';
import entityModule from './entityModule';

describe('entity store module', () => {
  beforeEach(() => {
    // Reset module state before each test
  });

  describe('mutations', () => {
    it('sets items correctly', () => {
      const state = { items: [] };
      const items = [{ id: 1, name: 'Item 1' }];
      entityModule.mutations.setItems(state, items);
      expect(state.items).toEqual(items);
    });
  });

  describe('actions', () => {
    it('fetches items and commits mutation', async () => {
      const commit = jest.fn();
      const mockItems = [{ id: 1, name: 'Item 1' }];
      
      // Mock API call
      jest.spyOn(entityService, 'getAll').mockResolvedValue(mockItems);
      
      await entityModule.actions.fetchItems({ commit });
      expect(commit).toHaveBeenCalledWith('setItems', mockItems);
    });
  });

  describe('getters', () => {
    it('returns filtered items', () => {
      const state = { items: [{ id: 1, active: true }, { id: 2, active: false }] };
      const result = entityModule.getters.activeItems(state);
      expect(result).toHaveLength(1);
    });
  });
});
```

### Store Test Template (Pinia)

```typescript
import { setActivePinia, createPinia } from 'pinia';
import { useEntityStore } from './entityStore';

describe('entity store', () => {
  beforeEach(() => {
    setActivePinia(createPinia());
  });

  it('initializes with default state', () => {
    const store = useEntityStore();
    expect(store.items).toEqual([]);
    expect(store.loading).toBe(false);
  });

  describe('actions', () => {
    it('fetches items successfully', async () => {
      const store = useEntityStore();
      const mockItems = [{ id: 1, name: 'Item 1' }];
      
      jest.spyOn(entityService, 'getAll').mockResolvedValue(mockItems);
      
      await store.fetchItems();
      expect(store.items).toEqual(mockItems);
      expect(store.loading).toBe(false);
    });
  });

  describe('getters', () => {
    it('returns correct item count', () => {
      const store = useEntityStore();
      store.items = [{ id: 1 }, { id: 2 }];
      expect(store.itemCount).toBe(2);
    });
  });
});
```

### Test Coverage Requirements

- **Components**: >70% coverage (focus on user interactions, computed properties, methods)
- **Views**: >70% coverage (lifecycle, navigation, major workflows)
- **Stores**: >80% coverage (state mutations, actions, getters)
- **Utilities**: >80% coverage (all functions and edge cases)
- **Directives**: >70% coverage (behavior and DOM manipulation)

### Test Organization Best Practices

1. **Describe blocks**: Group related tests logically
   - Component/View name as top-level describe
   - Group by: lifecycle hooks, computed properties, methods, events
2. **Test naming**: Use descriptive test names that explain expected behavior
3. **AAA Pattern**: Arrange, Act, Assert in each test
4. **Mocking**: Mock external dependencies (API calls, router, store)
5. **Cleanup**: Use `afterEach` to clear mocks and reset state
6. **Isolation**: Each test should be independent and not rely on others

## Linting and Formatting

- ESLint 9 with flat config format (`eslint.config.cjs`)
- Prettier 3 for code formatting
- Vue ESLint plugin for Vue-specific rules
- TypeScript ESLint for TypeScript rules
- Pre-commit hooks via Husky for automated checks
