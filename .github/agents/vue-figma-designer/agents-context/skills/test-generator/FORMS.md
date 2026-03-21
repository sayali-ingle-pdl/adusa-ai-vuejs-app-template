# Test Generator Forms

## Test Configuration Form

### Testing Framework Setup
```yaml
testRunner:
  type: select
  required: true
  options: ["vitest", "jest", "mocha"]
  default: "vitest"
  description: "Primary test runner for the project"

testEnvironment:
  type: select
  required: true
  options: ["jsdom", "happy-dom", "node"]
  default: "jsdom"
  description: "Testing environment for component tests"

coverageProvider:
  type: select
  options: ["v8", "istanbul", "c8"]
  default: "v8"
  description: "Code coverage provider"
```

### Test Types to Generate
```yaml
testTypes:
  type: multiselect
  required: true
  options:
    - "unit"
    - "integration" 
    - "accessibility"
    - "visual-regression"
    - "performance"
    - "e2e"
  default: ["unit", "integration", "accessibility"]
  description: "Types of tests to generate"

componentTypes:
  type: multiselect
  options: ["components", "views", "stores", "services", "utils"]
  default: ["components", "views", "stores"]
  description: "Component types to test"
```

### Coverage Requirements
```yaml
coverageThresholds:
  global:
    type: number
    default: 80
    min: 0
    max: 100
    description: "Global coverage threshold percentage"
  
  statements:
    type: number
    default: 80
    min: 0
    max: 100
  
  branches:
    type: number
    default: 75
    min: 0
    max: 100
  
  functions:
    type: number
    default: 80
    min: 0
    max: 100
  
  lines:
    type: number
    default: 80
    min: 0
    max: 100

excludePatterns:
  type: array
  description: "File patterns to exclude from coverage"
  items:
    pattern:
      type: string
      placeholder: "**/*.d.ts"
```

## Component Testing Configuration

### Test Scenarios
```yaml
propsScenarios:
  type: boolean
  default: true
  description: "Generate tests for all prop combinations"

eventScenarios:
  type: boolean
  default: true
  description: "Test event emission and handling"

slotScenarios:
  type: boolean
  default: true
  description: "Test slot content and scoped slots"

errorBoundaries:
  type: boolean
  default: true
  description: "Test error handling scenarios"

loadingStates:
  type: boolean
  default: true
  description: "Test loading and async states"

edgeCases:
  type: boolean
  default: false
  description: "Generate edge case testing scenarios"
```

### Mocking Configuration
```yaml
mockStrategy:
  type: select
  options: ["auto", "manual", "hybrid"]
  default: "auto"
  description: "Approach for mocking dependencies"

mockAPIs:
  type: boolean
  default: true
  description: "Mock API calls in component tests"

mockRouting:
  type: boolean
  default: true
  description: "Mock Vue Router in tests"

mockStores:
  type: boolean
  default: true
  description: "Mock Pinia/Vuex stores"

globalMocks:
  type: array
  description: "Global mocks to apply to all tests"
  items:
    module:
      type: string
      description: "Module to mock"
    implementation:
      type: string
      description: "Mock implementation"
```

## Accessibility Testing

### A11y Test Configuration
```yaml
axeRules:
  type: multiselect
  options:
    - "color-contrast"
    - "keyboard-navigation"
    - "aria-attributes"
    - "semantic-html"
    - "focus-management"
    - "screen-reader"
  default: ["color-contrast", "keyboard-navigation", "aria-attributes"]

wcagLevel:
  type: select
  options: ["A", "AA", "AAA"]
  default: "AA"
  description: "WCAG compliance level to test"

screenReaderTesting:
  type: boolean
  default: false
  description: "Generate screen reader compatibility tests"

keyboardNavigation:
  type: boolean
  default: true
  description: "Test keyboard navigation patterns"
```

## Visual Regression Testing

### Visual Test Setup
```yaml
enableVisualTesting:
  type: boolean
  default: false
  description: "Generate visual regression tests"

visualTool:
  type: select
  options: ["chromatic", "percy", "playwright-screenshots"]
  default: "playwright-screenshots"
  description: "Visual testing tool to use"

viewports:
  type: multiselect
  options: ["mobile", "tablet", "desktop", "large-desktop"]
  default: ["mobile", "desktop"]
  description: "Viewports to test for visual regression"

browsers:
  type: multiselect
  options: ["chromium", "firefox", "webkit"]
  default: ["chromium"]
  description: "Browsers for visual testing"

thresholdPixels:
  type: number
  default: 100
  description: "Pixel difference threshold for visual tests"
```

## Performance Testing

### Performance Metrics
```yaml
performanceTesting:
  type: boolean
  default: false
  description: "Generate performance tests"

renderingThreshold:
  type: number
  default: 100
  description: "Maximum rendering time in milliseconds"

memoryLeakDetection:
  type: boolean
  default: true
  description: "Test for memory leaks"

bundleSizeTracking:
  type: boolean
  default: false
  description: "Track component bundle size impact"
```

## E2E Testing Configuration

### E2E Framework
```yaml
e2eFramework:
  type: select
  options: ["playwright", "cypress", "webdriver"]
  default: "playwright"
  description: "End-to-end testing framework"

e2eEnvironments:
  type: multiselect
  options: ["local", "staging", "production"]
  default: ["local"]
  description: "Environments to run E2E tests"

pageObjects:
  type: boolean
  default: true
  description: "Generate Page Object Model patterns"

crossBrowser:
  type: boolean
  default: false
  description: "Run E2E tests across multiple browsers"
```