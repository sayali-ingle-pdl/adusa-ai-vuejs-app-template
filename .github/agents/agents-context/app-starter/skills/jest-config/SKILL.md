# Jest Config Skill

## Purpose
Generate the `jest.config.cjs` file for unit testing configuration.

## Output
Create the file: `jest.config.cjs`

## Template

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

## Notes
- Coverage targets exclude infrastructure files
- Module name mapper handles path aliases and asset imports
- Setup files configure test environment
- Transform ignore patterns allow testing of ES modules like axios
