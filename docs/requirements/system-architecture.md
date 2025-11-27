# System Architecture for OmniInventoryManagerWeb

## 1. Overview

OmniInventoryManagerWeb is a Vue 3 micro frontend application built with Vite and TypeScript. It follows the single-spa architecture pattern to enable independent deployment and runtime integration with other micro frontends in the omni-fulfillment platform.

### Architecture Principles

- **Micro Frontend Architecture**: Self-contained application that can be developed, tested, and deployed independently
- **Component-Based Design**: Reusable Vue components following composition API patterns
- **State Management**: Centralized state using Vuex for predictable data flow
- **Type Safety**: Full TypeScript integration for compile-time error checking
- **CSS Isolation**: Scoped styles using unique application IDs to prevent style conflicts
- **API-First Design**: Clear separation between frontend and backend through REST APIs

## 2. Technology Stack

### Core Technologies

- **Vue 3.5+**: Progressive JavaScript framework with Composition API
- **Vite 6+**: Next-generation frontend build tool with fast HMR
- **TypeScript 4.9+**: Typed superset of JavaScript
- **Vue Router 4**: Official routing library for Vue 3
- **Vuex 4**: State management pattern + library
- **Single-SPA 2+**: Framework for building micro frontends

### Development Tools

- **ESLint 9**: Pluggable linting utility for JavaScript and TypeScript
- **Prettier 3**: Opinionated code formatter
- **Jest 27**: Testing framework
- **Vue Test Utils 2**: Official testing utilities for Vue components
- **Husky**: Git hooks for pre-commit checks
- **Sass**: CSS preprocessor for advanced styling

### Build Output

- **Format**: SystemJS (for single-spa integration)
- **Target**: ESNext
- **Source Maps**: Enabled for debugging
- **CSS Injection**: Styles injected via JavaScript with unique style tag ID

## 3. Application Structure

### Directory Organization

```
src/
├── App.vue                 # Root component
├── main.ts                 # Single-spa lifecycle exports
├── assets/                 # Static assets (images, fonts, etc.)
├── components/             # Reusable Vue components
│   ├── common/            # Shared components across features
│   └── [feature]/         # Feature-specific components
├── views/                  # Page-level components (routes)
├── router/                 # Vue Router configuration
│   └── index.ts           # Router setup with navigation guards
├── store/                  # Vuex state management
│   ├── index.ts           # Store configuration
│   └── modules/           # Vuex modules by feature
├── services/               # API service layer
│   └── [service].ts       # HTTP clients for backend APIs
├── interfaces/             # TypeScript type definitions
│   └── [entity].ts        # Entity and DTO interfaces
├── directives/             # Custom Vue directives
├── shared/                 # Shared utilities and constants
│   ├── constants/         # Application constants
│   ├── EnvConsts.ts       # Environment variable exports
│   └── utils/             # Helper functions
└── theme/                  # SCSS variables and mixins
    ├── vars.scss          # Theme variables
    ├── shadow.scss        # Shadow mixins
    └── index.scss         # Theme entry point
```

### Configuration Files

- `vite.config.ts`: Vite build configuration
- `tsconfig.json`: TypeScript compiler options
- `eslint.config.cjs`: ESLint rules and plugins
- `jest.config.cjs`: Jest testing configuration
- `package.json`: Dependencies and scripts
- `.env.local`: Local environment variables (gitignored)

## 4. Single-SPA Integration

### Lifecycle Methods

The application exports three lifecycle methods required by single-spa:

- **bootstrap**: Initializes the Vue application instance
- **mount**: Mounts the application to the DOM and restores cached styles
- **unmount**: Cleans up and removes the application from the DOM

### CSS Isolation Strategy

Styles are injected with a unique style tag ID matching the application ID. The style tag content is cached on first mount and restored on subsequent mounts to prevent duplicate style injection.

### Module Loading

The application is built as a SystemJS module to ensure compatibility with the single-spa root configuration and other micro frontends.

## 5. Routing Architecture

### Hash-based Routing

The application uses hash-based routing (`createWebHashHistory`) for compatibility with single-spa's route matching.

### Navigation Guards

Three global navigation guards are implemented:

1. **stripAccessToken**: Extracts and stores OAuth access tokens from query parameters
2. **ifAuthenticated**: Ensures user is authenticated before accessing protected routes
3. **destructureQueryParams**: Processes and stores query parameters in the store

### Route Structure

```
/
├── /home                  # Home/Dashboard view
├── /inventory             # Inventory list view
├── /inventory/:id         # Inventory detail view
├── /settings              # Settings view
└── /*                     # 404 Not Found view
```

## 6. State Management

### Vuex Store Structure

```
store/
├── index.ts               # Root store configuration
└── modules/
    ├── auth.ts            # Authentication state
    ├── inventory.ts       # Inventory data and filters
    └── app.ts             # Application-wide state
```

### State Organization Principles

- **Modules**: Feature-based state modules with namespacing
- **Mutations**: Synchronous state changes only
- **Actions**: Async operations and business logic
- **Getters**: Computed state derivations

## 7. API Integration

### Service Layer Pattern

Each backend API has a corresponding service file that encapsulates HTTP requests:

```typescript
// services/inventoryService.ts
import axios from 'axios';
import { InventoryItem, InventorySearchParams } from '@/interfaces/inventory';

export const inventoryService = {
  async searchInventory(params: InventorySearchParams): Promise<InventoryItem[]> {
    const response = await axios.get('/api/inventory/search', { params });
    return response.data;
  },
  
  async getInventoryItem(id: string): Promise<InventoryItem> {
    const response = await axios.get(`/api/inventory/${id}`);
    return response.data;
  }
};
```

### Environment-Based Configuration

API base URLs and other configuration values are managed through environment variables:

- Development: Vite dev server proxy for CORS handling
- Production: Relative URLs to deployed backend services

## 8. Component Architecture

### Component Categories

1. **Views**: Top-level route components (pages)
2. **Feature Components**: Components specific to a feature domain
3. **Common Components**: Reusable components across features
4. **Layout Components**: Structural components (headers, footers, sidebars)

### Component Design Patterns

- **Composition API**: Use `<script setup>` for component logic
- **Props Validation**: TypeScript interfaces for prop types
- **Emit Events**: Type-safe event definitions
- **Scoped Styles**: Component-specific styles with `<style scoped>`
- **Theme Variables**: Use SCSS variables from `@/theme/`

## 9. Testing Strategy

### Unit Testing

- **Framework**: Jest with Vue Test Utils
- **Coverage**: >80% for services, stores, and complex components
- **Mock Strategy**: Mock external dependencies (axios, router, store)
- **Test Location**: Co-located with source files (`*.spec.ts`)

### Test Configuration

```javascript
// jest.config.cjs
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  collectCoverageFrom: [
    'src/**/*.{ts,js,vue}',
    '!src/main.ts',
    '!src/App.vue',
    '!src/**/interfaces/*.ts'
  ],
  transform: {
    '^.+\\.vue$': '@vue/vue3-jest',
    '^.+\\.tsx?$': 'ts-jest'
  },
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1'
  }
};
```

## 10. Build and Deployment

### Build Configuration

- **Entry Point**: `src/main.ts`
- **Output Format**: SystemJS module
- **Output Location**: `dist/js/app.js`
- **Source Maps**: Generated for debugging
- **CSS**: Injected by JavaScript with style tag ID

### Docker Containerization

The application is containerized using nginx for serving static assets:

- Multi-stage build: Build stage + nginx runtime
- Environment variable injection at runtime
- Health check endpoints
- Optimized for production deployment

### Environment Variables

Required environment variables:

- `VITE_ACCESS_MANAGEMENT_BASE_URL`: Access manager service URL
- `VITE_LAUNCHER_APP_URL`: Launcher application URL
- `VITE_DATADOG_ENV`: Datadog environment (local/dev/prod)
- `VITE_DATADOG_APPLICATION_ID`: Datadog application ID
- `VITE_DATADOG_CLIENT_TOKEN`: Datadog client token
- `VITE_APP_VERSION`: Application version for tracking

## 11. Security Considerations

### Authentication Flow

1. User navigates to application via launcher
2. Access token provided via query parameter or session
3. Token extracted and stored by navigation guard
4. Token included in all API requests via axios interceptor

### Authorization

- Role-based access control managed by Access Manager service
- Frontend enforces UI-level permissions (hide/disable features)
- Backend APIs enforce actual authorization rules

### Content Security

- HTTPS only for production
- CORS configured via dev server proxy (development)
- Environment variables for sensitive configuration

## 12. Performance Optimization

### Bundle Optimization

- Code splitting by route using dynamic imports
- Tree shaking for unused code elimination
- Vendor chunking for better caching

### Runtime Performance

- Virtual scrolling for large lists
- Debounced search inputs
- Lazy loading of images and components
- Pagination for large datasets

### Caching Strategy

- Browser caching for static assets
- HTTP cache headers configured in nginx
- Service worker (future enhancement)

## 13. Monitoring and Observability

### Datadog RUM Integration

- Session replay recording (triggered manually)
- User interaction tracking
- Resource loading performance
- Long task tracking
- Feature flag tracking (experimental)

### Error Tracking

- JavaScript errors captured and sent to Datadog
- Network request failures logged
- User actions leading to errors recorded

## 14. Development Workflow

### Local Development

1. Install dependencies: `npm install`
2. Configure `.env.local` with required variables
3. Start dev server: `npm run serve` (integrated) or `npm run serve:standalone` (standalone)
4. Hot module replacement for fast iteration

### Code Quality Checks

- Pre-commit hooks via Husky
- Linting: `npm run lint`
- Formatting: `npm run prettier`
- Type checking: `tsc --noEmit`

### Building for Production

```bash
npm run build
```

Outputs optimized SystemJS bundle to `dist/js/app.js`

## 15. Standards and Conventions

### Naming Conventions

- **Files**: kebab-case (e.g., `inventory-list.vue`)
- **Components**: PascalCase (e.g., `InventoryList`)
- **Variables/Functions**: camelCase (e.g., `searchInventory`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `API_BASE_URL`)
- **Interfaces**: PascalCase with `I` prefix optional (e.g., `InventoryItem`)

### Code Organization

- One component per file
- Index files for clean imports
- Group related files by feature
- Co-locate tests with source files

### Git Workflow

- Feature branches from `main`
- Conventional commit messages
- Pull request reviews required
- Automated CI/CD pipeline

## 16. Future Considerations

### Planned Enhancements

- Pinia migration (replacing Vuex)
- Service worker for offline support
- GraphQL integration for complex queries
- Micro frontend communication bus
- Shared component library package
- E2E testing with Playwright
- Storybook for component documentation

### Scalability Roadmap

- Lazy loading of Vuex modules
- Route-based code splitting optimization
- CDN integration for static assets
- Server-side rendering (SSR) evaluation
- Progressive Web App (PWA) features
