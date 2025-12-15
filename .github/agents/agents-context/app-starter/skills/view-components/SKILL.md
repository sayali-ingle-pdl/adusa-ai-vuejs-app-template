# View Components Skill

## Purpose
Generate initial view components for the application **WITH UNIT TESTS**. Each component MUST have a corresponding .spec.ts file created at the same time as the component.

**CRITICAL**: This skill requires creating BOTH the .vue component file AND its .spec.ts test file. Do not skip test file creation.

## Output
Create the files:
- `src/views/Home/Home.vue`
- `src/views/Home/Home.spec.ts` ⚠️ **REQUIRED**
- `src/views/PageNotFoundView/PageNotFoundView.vue`
- `src/views/PageNotFoundView/PageNotFoundView.spec.ts` ⚠️ **REQUIRED**

## Requirements
- Home view displays a welcome message
- PageNotFoundView provides a 404 error page
- **Each component MUST have a unit test file within its directory**
- Unit tests MUST cover:
  - Component rendering
  - Props (if any)
  - Events/emits (if any)
  - Methods and computed properties
  - Lifecycle hooks (created, mounted, etc.)
  - User interactions (clicks, inputs, etc.)
  - Conditional rendering
- Both components use theme variables for styling
- Components are placed in their own directories for organization

## Test Coverage Requirements
Each .spec.ts file should include:
- Basic rendering test
- DOM element verification (headings, paragraphs, links, etc.)
- Text content verification
- CSS class verification
- Router/navigation tests (if applicable)
- Minimum 80% code coverage for each component

## Execution Checklist
Use this checklist to ensure all required files are created:
- [ ] Create `src/views/Home/Home.vue` component
- [ ] Create `src/views/Home/Home.spec.ts` with comprehensive tests
- [ ] Create `src/views/PageNotFoundView/PageNotFoundView.vue` component  
- [ ] Create `src/views/PageNotFoundView/PageNotFoundView.spec.ts` with comprehensive tests
- [ ] Verify all 4 files exist in correct directories
- [ ] Run unit test to ensure tests pass
- [ ] Verify test output shows tests for both components

## Validation
After creating all files, validate:
1. ✓ Both .vue files exist in correct directories
2. ✓ Both .spec.ts files exist alongside their respective components
3. ✓ Running unit test confirms all tests pass
4. ✓ Test output shows at least 3-5 tests per component
5. ✓ No test files are skipped or marked as pending
