# Test Generator API Reference

## Core Test Generation Functions

### Component Test Generation
```typescript
function generateComponentTest(
  component: GeneratedComponent,
  options: ComponentTestOptions
): ComponentTestSuite

interface ComponentTestOptions {
  testRunner: 'vitest' | 'jest' | 'mocha'
  includeAccessibility: boolean
  includeVisualRegression: boolean
  mockStrategy: 'auto' | 'manual' | 'hybrid'
  coverageRequired: boolean
}

interface ComponentTestSuite {
  name: string
  filePath: string
  imports: string[]
  setup: string
  teardown: string
  tests: TestCase[]
  mocks: MockDefinition[]
}
```

### Store Test Generation
```typescript
function generateStoreTest(
  store: StoreDefinition,
  options: StoreTestOptions
): StoreTestSuite

interface StoreTestOptions {
  framework: 'pinia' | 'vuex'
  testActions: boolean
  testGetters: boolean
  testMutations: boolean
  mockAPIs: boolean
}
```

### API Service Test Generation
```typescript
function generateServiceTest(
  service: ApiServiceDefinition,
  options: ServiceTestOptions
): ServiceTestSuite

interface ServiceTestOptions {
  mockStrategy: 'msw' | 'nock' | 'manual'
  testErrorHandling: boolean
  testRetryLogic: boolean
  testRateLimiting: boolean
}
```

## Test Case Structures

### Component Test Case
```typescript
interface ComponentTestCase {
  name: string
  type: 'mount' | 'props' | 'events' | 'slots' | 'accessibility'
  setup: string
  assertions: Assertion[]
  cleanup?: string
}

interface Assertion {
  type: 'expect' | 'toHaveBeenCalled' | 'toEmit' | 'toHaveClass'
  target: string
  matcher: string
  expected?: any
}
```

### Accessibility Test Case
```typescript
interface AccessibilityTestCase {
  name: string
  axeConfig: AxeConfiguration
  keyboardTests: KeyboardTest[]
  screenReaderTests: ScreenReaderTest[]
  colorContrastTests: ContrastTest[]
}

interface KeyboardTest {
  scenario: string
  keySequence: string[]
  expectedBehavior: string
  assertions: string[]
}
```

### Visual Regression Test
```typescript
interface VisualRegressionTest {
  name: string
  component: string
  variants: VariantTest[]
  viewports: ViewportConfig[]
  threshold: number
}

interface VariantTest {
  name: string
  props: Record<string, any>
  slots?: Record<string, string>
  state?: string
}
```

## Mock Configurations

### Component Mocking
```typescript
interface ComponentMockConfig {
  childComponents: ComponentMock[]
  globalComponents: GlobalMock[]
  directives: DirectiveMock[]
  plugins: PluginMock[]
}

interface ComponentMock {
  name: string
  template: string
  props?: string[]
  emits?: string[]
}
```

### API Mocking with MSW
```typescript
interface MSWMockConfig {
  handlers: RequestHandler[]
  baseURL: string
  errorScenarios: ErrorScenario[]
}

interface RequestHandler {
  method: 'GET' | 'POST' | 'PUT' | 'DELETE' | 'PATCH'
  path: string
  response: any
  delay?: number
  status?: number
}

interface ErrorScenario {
  name: string
  condition: string
  response: {
    status: number
    body: any
  }
}
```

### Store Mocking (Pinia)
```typescript
interface PiniaTestConfig {
  stores: PiniaStoreMock[]
  initialState: Record<string, any>
  stubActions: boolean
}

// Runner-agnostic mock function type for store actions
type PiniaMockFn = (...args: any[]) => any

interface PiniaStoreMock {
  name: string
  state: Record<string, any>
  actions: Record<string, PiniaMockFn>
  getters: Record<string, any>
}
```

## Test Utilities and Helpers

### Component Testing Utilities
```typescript
// Custom mounting function with common setup
function mountComponent(
  component: Component,
  options?: MountOptions
): VueWrapper

// Props testing helper
function testAllPropCombinations(
  component: Component,
  propDefinitions: PropDefinition[]
): TestCase[]

// Event testing helper
function testEventEmission(
  wrapper: VueWrapper,
  eventName: string,
  trigger: () => Promise<void>,
  expectedPayload?: any
): Promise<void>
```

### Accessibility Testing Helpers
```typescript
// Axe testing helper
function expectNoA11yViolations(
  element: HTMLElement,
  axeConfig?: AxeConfiguration
): Promise<void>

// Keyboard navigation helper
function testKeyboardNavigation(
  wrapper: VueWrapper,
  keySequence: string[]
): Promise<FocusResult[]>

// Screen reader announcement helper
function expectScreenReaderAnnouncement(
  wrapper: VueWrapper,
  expectedText: string
): void
```

### Performance Testing Utilities
```typescript
// Render performance testing
function measureRenderTime(
  component: Component,
  props: Record<string, any>
): Promise<PerformanceMetrics>

interface PerformanceMetrics {
  renderTime: number
  memoryUsage: number
  rerenderCount: number
}

// Memory leak detection
function detectMemoryLeaks(
  testFunction: () => Promise<void>
): Promise<MemoryLeakReport>
```

## Test Configuration Schema

### Jest/Vitest Configuration
```typescript
interface TestConfig {
  testEnvironment: 'jsdom' | 'happy-dom' | 'node'
  setupFilesAfterEnv: string[]
  testMatch: string[]
  transform: Record<string, string>
  moduleNameMapper: Record<string, string>
  collectCoverageFrom: string[]
  coverageThreshold: CoverageThreshold
  globals: Record<string, any>
}

interface CoverageThreshold {
  global: {
    statements: number
    branches: number
    functions: number
    lines: number
  }
}
```

### Vue Test Utils Configuration
```typescript
interface VueTestConfig {
  global: {
    components: Record<string, Component>
    directives: Record<string, Directive>
    plugins: Plugin[]
    mocks: Record<string, any>
    stubs: Record<string, any>
  }
}
```

## Assertion Patterns

### Component Assertions
```typescript
// DOM assertions
expect(wrapper.find('.button')).toBeTruthy()
expect(wrapper.text()).toContain('Expected text')
expect(wrapper.classes()).toContain('active')
expect(wrapper.attributes('aria-label')).toBe('Expected label')

// Vue-specific assertions
expect(wrapper.vm.computedProperty).toBe('expected value')
expect(wrapper.emitted()).toHaveProperty('eventName')
expect(wrapper.emitted('click')).toHaveLength(2)

// Async assertions
await wrapper.vm.$nextTick()
expect(wrapper.find('.loading')).toBeFalsy()
```

### Store Assertions
```typescript
// Pinia assertions
expect(store.state).toEqual(expectedState)
expect(store.getterName).toBe(expectedValue)
expect(mockAction).toHaveBeenCalledWith(expectedArgs)

// State mutation assertions
const initialState = { ...store.$state }
await store.actionName()
expect(store.$state).not.toEqual(initialState)
```

### API Service Assertions
```typescript
// HTTP request assertions
expect(mockAxios.get).toHaveBeenCalledWith('/api/users')
expect(mockAxios.post).toHaveBeenCalledWith('/api/users', expectedData)

// Response handling assertions
const result = await userService.getUser(1)
expect(result).toEqual(expectedUser)

// Error handling assertions
mockAxios.get.mockRejectedValueOnce(new Error('Network error'))
await expect(userService.getUser(1)).rejects.toThrow('Network error')
```

## Best Practices and Patterns

### Test Organization
```typescript
describe('ComponentName', () => {
  describe('Props', () => {
    // Prop-related tests
  })

  describe('Events', () => {
    // Event emission tests
  })

  describe('Slots', () => {
    // Slot content tests
  })

  describe('Accessibility', () => {
    // A11y tests
  })

  describe('Error States', () => {
    // Error boundary tests
  })
})
```

### Common Test Patterns
1. **AAA Pattern** - Arrange, Act, Assert
2. **Given-When-Then** - BDD style testing
3. **Page Object Model** - For E2E tests
4. **Factory Pattern** - For test data generation
5. **Builder Pattern** - For complex component setup