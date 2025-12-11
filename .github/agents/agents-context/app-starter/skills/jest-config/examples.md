# Jest Configuration Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains templates and detailed examples.

## Basic Template

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  collectCoverage: true,
  collectCoverageFrom: [
    '!**/tests/**',
    '!**/node_modules/**',
    '**/src/**/*.{ts,js,vue}',
    '!**/src/**/interfaces/*.ts',
    '!**/src/**/router/*.ts',
    '!**/src/**/services/**/*.ts',
    '!**/src/**/shared/constants/**/*.ts',
    '!**/src/**/shared/EnvConsts.ts',
    '!**/src/store/index.ts',
    '!**/src/App.vue',
    '!**/src/main.ts',
    '!**/src/shims-vue.d.ts',
  ],
  testMatch: [
    '**/src/**/**/*.spec.{ts,js,vue}',
    '**/tests/**/*.spec.{ts,js,vue}',
  ],
  moduleFileExtensions: ['ts', 'js', 'json', 'vue'],
  transform: {
    '^.+\\.vue$': '@vue/vue3-jest',
    '^.+\\.tsx?$': 'ts-jest',
    '^.+\\.js$': 'babel-jest',
  },
  testPathIgnorePatterns: [
    '/node_modules/',
    '/dist/',
    'src/shared/EnvConsts.ts',
  ],
  transformIgnorePatterns: ['node_modules/(?!axios)'],
  setupFiles: ['<rootDir>/tests/unit/testSetup.ts'],
  moduleNameMapper: {
    '^@/shared/EnvConsts$': '<rootDir>/tests/unit/mockEnvConsts.ts',
    '\\.svg$': '<rootDir>/tests/unit/svgMock.cjs',
    '^@/(.*)$': '<rootDir>/src/$1',
    '\\.(jpg|jpeg|png|gif|eot|otf|webp|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$':
      '<rootDir>/tests/unit/fileMock.cjs',
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
  coverageProvider: 'v8',
};
```

## Additional Files Required

### tests/unit/testSetup.ts
```typescript
// Test setup file
global.console.warn = jest.fn();
global.console.error = jest.fn();
```

### tests/unit/mockEnvConsts.ts
```typescript
export const ddAppId = 'test-app-id';
export const ddClientToken = 'test-token';
export const ddEnv = 'test';
export const appVersion = '0.0.0';
export const accessManagementBaseUrl = 'http://test.com';
export const launcherAppUrl = 'http://launcher.test.com';
```

### tests/unit/svgMock.cjs
```javascript
module.exports = 'svg-mock';
```

### tests/unit/fileMock.cjs
```javascript
module.exports = 'file-mock';
```

## What Each Configuration Does
### Basic Setup
#### `preset: 'ts-jest'`
- Pre-configured Jest setup for TypeScript
- Includes TypeScript transformation
- Provides type checking during tests
- Automatically configures ts-jest transformer
#### `testEnvironment: 'jsdom'`
- Simulates browser environment in Node.js
- Provides `window`, `document`, `navigator`, etc.
- Required for Vue component testing
- Alternative: `'node'` for backend testing
#### `collectCoverage: true`
- Automatically collects code coverage on every test run
- Generates coverage reports in `coverage/` directory
- Shows percentage of code tested
- Can be overridden with `--coverage=false` flag
### Coverage Collection
#### `collectCoverageFrom`
collectCoverageFrom: [
  '!**/tests/**',              // Exclude test files
  '!**/node_modules/**',       // Exclude dependencies
  '**/src/**/*.{ts,js,vue}',   // Include all source files
  '!**/src/**/interfaces/*.ts', // Exclude type definitions
  // ... more exclusions
]
**Included in coverage:**
- All `.ts`, `.js`, `.vue` files in `src/`
**Excluded from coverage:**
- **Test files**: `tests/**` - Don't measure test code
- **Dependencies**: `node_modules/**` - Third-party code
- **Interfaces**: Type definitions don't need testing
- **Router**: Configuration files, not logic
- **Services**: API clients (integration tested separately)
- **Constants**: Static data files
- **EnvConsts.ts**: Environment variable exports
- **Store index**: Vuex initialization boilerplate
- **App.vue**: Root component (integration tested)
- **main.ts**: Entry point (integration tested)
- **Type declarations**: `.d.ts` files
**Why exclude these:**
- Focus coverage on business logic and components
- Avoid false coverage metrics
- Infrastructure code tested differently
### Test Discovery
#### `testMatch`
testMatch: [
  '**/src/**/**/*.spec.{ts,js,vue}',
  '**/tests/**/*.spec.{ts,js,vue}',
]
**Finds tests in:**
- Source directory: `src/components/Button.spec.ts`
- Tests directory: `tests/unit/myTest.spec.ts`
- Any depth: `src/views/Home/components/List.spec.ts`
**Naming convention:**
- Must end with `.spec.ts`, `.spec.js`, or `.spec.vue`
- Example: `userService.spec.ts`, `Header.spec.ts`
#### `moduleFileExtensions`
moduleFileExtensions: ['ts', 'js', 'json', 'vue']
- Allows importing files without extensions
- Jest will try these extensions in order
- Supports TypeScript, JavaScript, JSON, and Vue files
### File Transformations
#### `transform`
transform: {
  '^.+\\.vue$': '@vue/vue3-jest',
  '^.+\\.tsx?$': 'ts-jest',
  '^.+\\.js$': 'babel-jest',
}
**Transformers:**
- **`@vue/vue3-jest`**: Transforms `.vue` single-file components
  - Handles `<template>`, `<script>`, `<style>` blocks
  - Extracts and processes each section
  
- **`ts-jest`**: Transforms TypeScript files
  - Compiles `.ts` and `.tsx` to JavaScript
  - Provides type checking
  
- **`babel-jest`**: Transforms JavaScript files
  - Uses Babel configuration (babel.config.cjs)
  - Handles modern syntax
### Test Path Ignores
#### `testPathIgnorePatterns`
testPathIgnorePatterns: [
  '/node_modules/',
  '/dist/',
  'src/shared/EnvConsts.ts',
]
- Directories Jest won't look for tests in
- `node_modules/`: Third-party code
- `dist/`: Build output
- `EnvConsts.ts`: Environment variables (mocked)
### Transform Ignores
#### `transformIgnorePatterns`
transformIgnorePatterns: ['node_modules/(?!axios)']
- **Default**: Don't transform anything in `node_modules/`
- **Exception**: DO transform `axios`
- **Why**: axios uses ES modules, needs transformation for Jest
**Pattern breakdown:**
- `node_modules/`: Match node_modules directory
- `(?!axios)`: Negative lookahead - except axios
- Result: Transform axios but not other node_modules
### Setup Files
#### `setupFiles`
setupFiles: ['<rootDir>/tests/unit/testSetup.ts']
- Runs before each test file
- Configures global test environment
- Mocks console warnings/errors
- `<rootDir>`: Jest variable for project root
**testSetup.ts content:**
global.console.warn = jest.fn();
global.console.error = jest.fn();
- Prevents console spam in tests
- Can still assert on console calls
### Module Name Mapper
#### `moduleNameMapper`
moduleNameMapper: {
  '^@/shared/EnvConsts$': '<rootDir>/tests/unit/mockEnvConsts.ts',
  '\\.svg$': '<rootDir>/tests/unit/svgMock.cjs',
  '^@/(.*)$': '<rootDir>/src/$1',
  '\\.(jpg|jpeg|png|...)$': '<rootDir>/tests/unit/fileMock.cjs',
  '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
}
**Purpose:** Maps module imports to mock files or transformed paths
#### 1. Mock EnvConsts
'^@/shared/EnvConsts$': '<rootDir>/tests/unit/mockEnvConsts.ts'
- Intercepts `import { EnvConsts } from '@/shared/EnvConsts'`
- Returns test values instead of runtime placeholders
- Prevents `${VITE_...}` placeholder issues
**mockEnvConsts.ts:**
export const ddAppId = 'test-app-id';
export const ddClientToken = 'test-token';
export const ddEnv = 'test';
export const appVersion = '0.0.0';
export const accessManagementBaseUrl = 'http://test.com';
export const launcherAppUrl = 'http://launcher.test.com';
#### 2. SVG Mock
'\\.svg$': '<rootDir>/tests/unit/svgMock.cjs'
- Mocks SVG file imports
- Returns simple string instead of SVG content
**svgMock.cjs:**
module.exports = 'svg-mock';
#### 3. Path Alias
'^@/(.*)$': '<rootDir>/src/$1'
- Maps `@/` to `src/` directory
- Example: `@/components/Button` → `src/components/Button`
- Matches project's import aliases
#### 4. File Mock
'\\.(jpg|jpeg|png|gif|...)$': '<rootDir>/tests/unit/fileMock.cjs'
- Mocks image and media file imports
- Prevents Jest from trying to parse binary files
**fileMock.cjs:**
module.exports = 'file-mock';
#### 5. CSS Mock
'\\.(css|less|scss|sass)$': 'identity-obj-proxy'
- Mocks CSS/SCSS imports
- Returns class names as-is
- Allows testing className props
**How it works:**
// Component
import styles from './Button.module.scss';
console.log(styles.primary); // 'primary'
// Test
expect(wrapper.classes()).toContain('primary');
### Coverage Provider
#### `coverageProvider: 'v8'`
- Uses V8 JavaScript engine's built-in coverage
- Faster than default Babel coverage
- More accurate coverage data
- Native to Node.js
## Installation
### Required Dependencies
npm install --save-dev \
  jest \
  ts-jest \
  @vue/vue3-jest \
  babel-jest \
  @types/jest \
  identity-obj-proxy \
  jsdom
### package.json
{
  "devDependencies": {
    "jest": "^29.7.0",
    "ts-jest": "^29.1.0",
    "@vue/vue3-jest": "^29.2.0",
    "babel-jest": "^29.7.0",
    "@types/jest": "^29.5.0",
    "identity-obj-proxy": "^3.0.0",
    "jsdom": "^22.1.0"
  }
}
## Required Mock Files
### tests/unit/testSetup.ts
// Suppress console warnings in tests
global.console.warn = jest.fn();
global.console.error = jest.fn();
// Add custom matchers if needed
expect.extend({
  // custom matchers
});
### tests/unit/mockEnvConsts.ts
export const ddAppId = 'test-app-id';
export const ddClientToken = 'test-token';
export const ddEnv = 'test';
export const appVersion = '0.0.0';
export const accessManagementBaseUrl = 'http://test.com';
export const launcherAppUrl = 'http://launcher.test.com';
### tests/unit/svgMock.cjs
module.exports = 'svg-mock';
### tests/unit/fileMock.cjs
module.exports = 'file-mock';
### Directory Structure
tests/
└── unit/
    ├── testSetup.ts
    ├── mockEnvConsts.ts
    ├── svgMock.cjs
    └── fileMock.cjs
## Usage
### Run All Tests
npm test
### Run Tests in Watch Mode
npm test -- --watch
### Run Specific Test File
npm test -- src/components/Button.spec.ts
### Run Tests Without Coverage
npm test -- --coverage=false
### Update Snapshots
npm test -- -u
## package.json Scripts
{
  "scripts": {
    "test": "jest",
    "test:unit": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:debug": "node --inspect-brk node_modules/.bin/jest --runInBand"
  }
}
## Writing Tests
### Component Test Example
// src/components/Button.spec.ts
import { mount } from '@vue/test-utils';
import Button from './Button.vue';
describe('Button', () => {
  it('renders button text', () => {
    const wrapper = mount(Button, {
      props: { text: 'Click Me' }
    });
    expect(wrapper.text()).toBe('Click Me');
  });
  it('emits click event', async () => {
    const wrapper = mount(Button);
    await wrapper.trigger('click');
    expect(wrapper.emitted('click')).toBeTruthy();
  });
});
### Service Test Example (Mocked)
// src/services/userService.spec.ts
import axios from 'axios';
import { userService } from './userService';
jest.mock('axios');
const mockedAxios = axios as jest.Mocked<typeof axios>;
describe('userService', () => {
  it('fetches users', async () => {
    mockedAxios.get.mockResolvedValue({ data: [{ id: 1, name: 'John' }] });
    
    const users = await userService.getAll();
    
    expect(users).toHaveLength(1);
    expect(users[0].name).toBe('John');
  });
});
### Store Test Example
// src/store/modules/user.spec.ts
import { createStore } from 'vuex';
import userModule from './user';
describe('user store', () => {
  it('sets user data', () => {
    const store = createStore({
      modules: { user: userModule }
    });
    
    store.commit('user/setUser', { id: 1, name: 'John' });
    
    expect(store.state.user.currentUser.name).toBe('John');
  });
});
## Coverage Reports
### Coverage Output
coverage/
├── lcov-report/
│   └── index.html       # HTML coverage report
├── coverage-final.json  # JSON coverage data
└── lcov.info           # LCOV format for CI tools
### View Coverage Report
npm test
open coverage/lcov-report/index.html
### Coverage Thresholds
Add to jest.config.cjs:
module.exports = {
  // ... other config
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
## Understanding Coverage Exclusions
### Why Exclude Interfaces?
'!**/src/**/interfaces/*.ts'
- Type definitions have no runtime code
- No logic to test
- 100% coverage not meaningful
### Why Exclude Router?
'!**/src/**/router/*.ts'
- Route configuration is declarative
- Tested through integration/e2e tests
- Component-level tests more valuable
### Why Exclude Services?
'!**/src/**/services/**/*.ts'
- API clients are thin wrappers
- Should be tested with integration tests
- Mocking API calls in unit tests has limited value
- Real value in testing actual API integration
### Why Exclude Constants?
'!**/src/**/shared/constants/**/*.ts'
- Static data exports
- No logic to test
- Values verified through usage
### Why Exclude EnvConsts?
'!**/src/**/shared/EnvConsts.ts'
- Runtime environment variable exports
- Mocked in tests (see moduleNameMapper)
- Tested through integration
### Why Exclude Store Index?
'!**/src/store/index.ts'
- Vuex store initialization
- No business logic
- Store modules tested individually
### Why Exclude App.vue and main.ts?
'!**/src/App.vue',
'!**/src/main.ts'
- Application entry points
- Bootstrap code
- Better tested with e2e tests
## Transform Patterns Explained
### Vue Files
'^.+\\.vue$': '@vue/vue3-jest'
**Transforms:**
<template>
  <div>{{ msg }}</div>
</template>
<script setup lang="ts">
const msg = 'Hello';
</script>
**Into:** JavaScript test code
### TypeScript Files
'^.+\\.tsx?$': 'ts-jest'
**Transforms:**
const greet = (name: string): string => {
  return `Hello, ${name}`;
};
**Into:** JavaScript (types removed)
### JavaScript Files
'^.+\\.js$': 'babel-jest'
Uses babel.config.cjs to transform modern syntax.
## Troubleshooting
### Issue: Cannot find module '@/...'
**Solution:**
Check moduleNameMapper has alias:
'^@/(.*)$': '<rootDir>/src/$1'
### Issue: SyntaxError: Cannot use import statement
**Solution:**
1. Verify babel.config.cjs exists
2. Check babel-jest is installed
3. Add package to transformIgnorePatterns exception
### Issue: Vue component not rendering
**Solution:**
1. Ensure `@vue/vue3-jest` is installed
2. Check transform for `.vue` files
3. Verify testEnvironment is `jsdom`
### Issue: Coverage not collected
**Solution:**
1. Check collectCoverage is `true`
2. Verify collectCoverageFrom patterns
3. Run with `--coverage` flag
### Issue: Tests fail with asset imports
**Solution:**
1. Check moduleNameMapper has mocks for images/styles
2. Verify mock files exist
3. Use identity-obj-proxy for CSS modules
## Best Practices
### 1. Co-locate Tests
src/
├── components/
│   ├── Button.vue
│   └── Button.spec.ts    ✅ Next to component
### 2. Meaningful Test Names
// Good
it('shows error message when validation fails', () => {});
// Bad
it('works', () => {});
### 3. Test Behavior, Not Implementation
// Good - tests behavior
expect(wrapper.text()).toContain('Welcome');
// Bad - tests implementation
expect(wrapper.vm.internalMethod).toHaveBeenCalled();
### 4. Use describe Blocks
describe('Button', () => {
  describe('when clicked', () => {
    it('emits event', () => {});
  });
  
  describe('when disabled', () => {
    it('does not emit event', () => {});
  });
});
### 5. Clean Up After Tests
afterEach(() => {
  jest.clearAllMocks();
});
## CI/CD Integration
### GitHub Actions
- name: Run tests
  run: npm test -- --coverage
  
- name: Upload coverage
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
### Coverage Badge
Add to README.md:
![Coverage](https://img.shields.io/codecov/c/github/org/repo)
## Advanced Configuration
### Parallel Testing
module.exports = {
  maxWorkers: '50%',  // Use half of CPU cores
};
### Test Timeout
module.exports = {
  testTimeout: 10000,  // 10 seconds
};
### Global Setup/Teardown
module.exports = {
  globalSetup: '<rootDir>/tests/globalSetup.ts',
  globalTeardown: '<rootDir>/tests/globalTeardown.ts',
};
## Resources
- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Vue Test Utils](https://test-utils.vuejs.org/)
- [ts-jest Configuration](https://kulshekhar.github.io/ts-jest/)
- [@vue/vue3-jest](https://github.com/vuejs/vue-jest)
- [Jest Coverage](https://jestjs.io/docs/configuration#collectcoveragefrom-array)
