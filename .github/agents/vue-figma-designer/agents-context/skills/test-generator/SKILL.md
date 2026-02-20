# Test Generator Skill

## Overview
Generates comprehensive test suites for Vue components, views, stores, and API services, including unit tests, integration tests, and accessibility tests using Vue Test Utils, Jest/Vitest, and specialized testing libraries.

## Capabilities
- Unit test generation for Vue components and composables
- Integration tests for views and store interactions
- API service tests with mock implementations
- Accessibility tests with automated a11y checking
- Visual regression tests for component variations
- E2E test foundations and page objects
- Performance testing for component rendering
- Cross-browser compatibility testing

## Usage
This skill automatically generates test suites based on component specifications and project testing configuration, following testing best practices and patterns.

## Key Functions
- `generateComponentTests()` - Creates comprehensive component test suites
- `generateStoreTests()` - Builds store and state management tests
- `generateServiceTests()` - Creates API service test coverage
- `generateAccessibilityTests()` - Adds a11y testing with axe-core
- `generateVisualTests()` - Creates visual regression test suites
- `generateE2ETests()` - Builds end-to-end testing foundations

## Test Types Generated
1. **Unit Tests** - Component props, events, computed properties
2. **Integration Tests** - Component interactions, store integration
3. **Accessibility Tests** - WCAG compliance, keyboard navigation
4. **Visual Tests** - Screenshot comparison, responsive layouts
5. **Performance Tests** - Rendering speed, memory usage
6. **API Tests** - Service methods, error handling, mocking

## Framework Support
- Vue Test Utils for component testing
- Jest/Vitest for test running and assertions
- Testing Library for user-centric testing
- Axe-core for accessibility validation
- MSW for API mocking
- Playwright/Cypress for E2E testing

## Quality Standards
- 80%+ code coverage requirements
- Component behavior verification
- Error boundary testing
- Loading state validation
- Responsive design testing