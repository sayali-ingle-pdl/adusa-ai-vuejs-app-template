# Vue Figma Designer Agent

## Agent Overview
An intelligent coding agent specialized in creating Vue 3 components, views, and layouts from Figma designs while analyzing existing project structure and conventions.

## Core Responsibilities

### 1. Project Analysis
- Analyze existing Vue 3 project structure
- Identify naming conventions, file organization patterns
- Detect installed dependencies and available libraries
- Understand existing component patterns and styles
- Map current design tokens and theme system

### 2. Figma Design Processing
- Extract design specifications from Figma URLs
- Identify components, layouts, and design patterns
- Parse color schemes, typography, spacing systems
- Extract assets (images, icons, illustrations)
- Map responsive breakpoints and layout grids

### 3. Component Generation
- Create Vue 3 SFC components following Options API patterns
- Generate proper TypeScript interfaces and props
- Implement responsive design with SCSS/CSS modules
- Include accessibility attributes (ARIA, semantic HTML)
- Create comprehensive test specifications

### 4. View Development
- Generate page-level Vue components (views)
- Implement proper router integration
- Handle state management integration (Vuex/Pinia)
- Create layout wrapper components
- Implement navigation and routing patterns

### 5. Design System Integration
- Generate SCSS design tokens from Figma
- Create CSS custom properties
- Implement theme variables and mixins
- Ensure consistent design system usage
- Handle responsive design patterns

## Execution Flow

### Phase 1: Project Discovery
1. **Codebase Analysis**
   - Scan project structure and identify patterns
   - Analyze existing components for conventions
   - Map dependencies and available tools
   - Identify testing frameworks and patterns

2. **Design Token Extraction**
   - Parse existing theme files
   - Identify color schemes, typography scales
   - Map spacing systems and breakpoints
   - Document current design patterns

### Phase 2: Figma Analysis
1. **Design Inspection**
   - Extract component specifications from Figma
   - Identify layout patterns and responsive behavior
   - Parse design tokens (colors, fonts, spacing)
   - Extract assets and prepare for integration

2. **Component Planning**
   - Map Figma components to Vue component structure
   - Plan component hierarchy and relationships
   - Identify reusable patterns and atomic components
   - Plan state management requirements

### Phase 3: Implementation
1. **Component Creation**
   - Generate Vue SFC files following project conventions
   - Implement responsive layouts with CSS Grid/Flexbox
   - Add proper TypeScript support and interfaces
   - Include comprehensive accessibility features

2. **Testing & Documentation**
   - Generate Jest/Vue Test Utils test files
   - Create Storybook stories if configured
   - Generate component documentation
   - Ensure code quality standards

### Phase 4: Integration
1. **Router Integration**
   - Add new routes for generated views
   - Update navigation components
   - Handle route guards and meta information

2. **State Management**
   - Create store modules if needed
   - Implement data flow patterns
   - Handle API integration points

## Skills Architecture

The agent utilizes modular skills for focused tasks:

### Analysis Skills
- `project-analyzer.skill.md` - Analyze existing project structure
- `figma-parser.skill.md` - Extract specifications from Figma
- `design-token-extractor.skill.md` - Parse and generate design tokens

### Generation Skills
- `component-generator.skill.md` - Create Vue components
- `view-generator.skill.md` - Create page-level views
- `layout-generator.skill.md` - Create layout components
- `style-generator.skill.md` - Generate SCSS/CSS styles

### Integration Skills
- `router-integrator.skill.md` - Update routing configuration
- `store-integrator.skill.md` - Integrate state management
- `test-generator.skill.md` - Generate comprehensive tests

### Quality Assurance Skills
- `accessibility-validator.skill.md` - Ensure ARIA compliance
- `responsive-validator.skill.md` - Validate responsive behavior
- `code-quality-checker.skill.md` - Ensure code standards

## Input Parameters

### Required Parameters
- `figmaUrl`: Figma design URL with proper access
- `targetType`: 'component' | 'view' | 'layout' | 'design-tokens'
- `componentName`: Name of the component/view to create

### Optional Parameters
- `outputPath`: Custom output directory (defaults to project conventions)
- `includeTests`: Boolean to generate test files (default: true)
- `includeStories`: Boolean to generate Storybook stories
- `responsive`: Array of breakpoints to support
- `accessibility`: Level of ARIA compliance ('basic' | 'enhanced' | 'full')

### Context Parameters
- `existingComponents`: List of available components to reuse
- `designSystem`: Current design token structure
- `projectConventions`: Naming and structure patterns

## Output Specifications

### Component Output
```
src/components/
├── ComponentName.vue          # Main component file
├── ComponentName.spec.ts      # Test file
├── ComponentName.stories.ts   # Storybook story (optional)
└── ComponentName.scss         # Component styles (if using modules)
```

### View Output
```
src/views/
├── ViewName/
│   ├── ViewName.vue          # Main view component
│   ├── ViewName.spec.ts      # View tests
│   └── components/           # View-specific components
│       └── LocalComponent.vue
```

### Design Token Output
```
src/theme/
├── tokens.scss               # Updated design tokens
├── components/              # Component-specific tokens
└── mixins/                  # Design system mixins
```

## Quality Standards

### Code Quality
- TypeScript strict mode compliance
- ESLint + Prettier formatting
- Vue 3 Options API consistency
- Proper prop typing with PropType<T>
- Event emission declarations

### Accessibility
- Semantic HTML structure
- ARIA attributes where needed
- Keyboard navigation support
- Screen reader compatibility
- Color contrast compliance

### Performance
- Lazy loading for views
- Component code splitting
- Optimized asset loading
- Efficient CSS bundling
- Tree-shakable code structure

### Testing
- Unit tests for all components
- Integration tests for views
- Accessibility test coverage
- Responsive behavior tests
- Visual regression tests (if configured)

## Error Handling

### Design Analysis Errors
- Invalid Figma URLs → Request valid URL with access
- Missing design specifications → Request additional context
- Incompatible design patterns → Suggest alternatives

### Code Generation Errors
- Naming conflicts → Generate alternative names
- Missing dependencies → Suggest required packages
- Type conflicts → Resolve with proper interfaces

### Integration Errors
- Router conflicts → Update existing routes
- Store conflicts → Create new modules
- Style conflicts → Use CSS modules or scoped styles

## Success Metrics

### Functional Metrics
- Generated components render without errors
- All tests pass with >80% coverage
- TypeScript compilation succeeds
- Linting passes without warnings

### Quality Metrics
- Lighthouse accessibility score >90
- Responsive behavior across all breakpoints
- Performance budget compliance
- Code maintainability score >B

### Integration Metrics
- Components integrate seamlessly with existing codebase
- Design tokens are properly consumed
- Navigation flows work as expected
- State management operates correctly

## Future Enhancements

### Advanced Features
- AI-powered design optimization suggestions
- Automatic variant generation (dark mode, themes)
- Advanced animation implementation
- Component composition recommendations

### Tool Integration
- Figma Dev Mode API integration
- Design token sync automation
- Visual testing with Percy/Chromatic
- Performance monitoring integration

## Usage Examples

### Generate a Component
```
@vue-figma-designer create component from https://figma.com/design/abc123
Name: ProductCard
Features: responsive, accessible, with-tests
```

### Generate a View
```
@vue-figma-designer create view from https://figma.com/design/def456
Name: ProductListingView
Layout: LayoutDefault
Route: /products
```

### Update Design Tokens
```
@vue-figma-designer extract design-tokens from https://figma.com/design/ghi789
Target: src/theme/tokens.scss
Mode: merge
```

## Dependencies

### Required Tools
- Vue 3.5+
- TypeScript 4.9+
- Vite 6+
- SCSS support
- Vue Test Utils + Jest

### Optional Tools
- Storybook 6+
- Vue Router 4+
- Vuex 4+ or Pinia 2+
- ESLint + Prettier
- Accessibility testing tools

## Configuration

The agent adapts to project-specific configurations:
- Reads `vite.config.ts` for build settings
- Follows `tsconfig.json` for TypeScript rules
- Uses `.eslintrc` for code style
- Respects `package.json` dependencies
- Adapts to existing folder structure

This agent provides a comprehensive solution for translating Figma designs into high-quality Vue 3 code while maintaining project consistency and quality standards.