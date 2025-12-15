# App Component Skill

## Purpose
Generate the `src/App.vue` root component **WITH UNIT TEST**. The component MUST have a corresponding .spec.ts file created at the same time.

**CRITICAL**: This skill requires creating BOTH the App.vue component file AND its App.spec.ts test file. Do not skip test file creation.

## Output
- Create the file: `src/App.vue`
- Create the unit test file: `src/App.spec.ts` ⚠️ **REQUIRED**

## Requirements
- Simple root component with router-view
- The `id` attribute should use the `appId` constant from global constants
- Imports theme variables for global styling
- No scoped styles to allow global CSS
- Component should be minimal and serve as a container for the router view
- **MUST have a unit test file in the same directory**
- Unit tests MUST cover:
  - Component rendering
  - Router-view presence
  - Props (if any)
  - Methods and computed properties (if any)
  - Lifecycle hooks (created, mounted, etc.)
  - Global store initialization (if applicable)

## Test Coverage Requirements
The App.spec.ts file should include:
- Basic rendering test
- Router-view component verification
- DOM element verification (root div with correct id)
- Lifecycle hook execution tests
- Store initialization tests (if using Pinia/Vuex)
- Minimum 80% code coverage

## Execution Checklist
Use this checklist to ensure all required files are created:
- [ ] Create `src/App.vue` component
- [ ] Create `src/App.spec.ts` with comprehensive tests
- [ ] Verify both files exist in src/ directory
- [ ] Run unit test to ensure tests pass
- [ ] Verify test output shows tests for App component

## Validation
After creating all files, validate:
1. ✓ App.vue exists in src/ directory
2. ✓ App.spec.ts exists alongside App.vue
3. ✓ Running unit test confirms all tests pass
4. ✓ Test output shows at least 3-5 tests for App component
5. ✓ No test files are skipped or marked as pending
