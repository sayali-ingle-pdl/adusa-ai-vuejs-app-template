# Jest Configuration Examples

## Example of jest.config.cjs

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

*** Important Notes ***
- The above is an example of the structure of jest.config.cjs that is desired. 
- The codes and configurations may be outdated.
- When reviewing the above example, make sure to codes and configuration to apply the latest best pratice.
- If any configuration is deprecated, apply the latest configuration that matches the outdated one.