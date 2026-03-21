# Project Analyzer Skill

## Overview
Analyzes the existing Vue 3 project structure, identifies available component libraries, extracts coding patterns, and maps reusable components to inform Figma-to-code generation.

## Capabilities
- **Scan project structure** and identify conventions
- **Detect installed component libraries** (Vuetify, Quasar, PrimeVue, Element Plus, Ant Design Vue)
- **Analyze existing components** for patterns and styles
- **Extract coding conventions** (naming, file structure, API style)
- **Map available UI components** from libraries and custom components
- **Identify form components**: inputs, dropdowns, buttons, checkboxes, filters, radio buttons, switches, date pickers, file uploads
- **Catalog interactive components**: tabs, accordions, tooltips, popovers, menus
- **Map data display components**: tables, lists, cards, chips, badges
- **Identify design tokens** and theme systems
- **Detect state management** patterns (Pinia/Vuex)
- **Analyze routing** structure and patterns

## Usage
This skill is automatically triggered before Figma parsing to understand the project context and available resources.

## Key Functions
- `analyzeProjectStructure()` - Scans project directories and identifies structure
- `detectComponentLibrary()` - Identifies installed UI component libraries
- `extractExistingComponents()` - Maps all existing custom components
- `analyzeComponentPatterns()` - Identifies coding patterns and conventions
- `extractDesignSystem()` - Parses theme files and design tokens
- `mapLibraryComponents()` - Creates mapping of available library components
- `catalogFormComponents()` - **Maps all form inputs, dropdowns, buttons, checkboxes, filters**
- `catalogInteractiveComponents()` - **Maps tabs, accordions, menus, tooltips**
- `catalogDataComponents()` - **Maps tables, lists, cards, grids**
- `generateComponentMapping()` - Maps Figma elements to existing components

## Component Library Detection

### Detection Strategy
1. **Check package.json dependencies**
   - Look for `vuetify`, `quasar`, `primevue`, `element-plus`, `ant-design-vue`
   - Identify versions and configurations

2. **Analyze main.ts/main.js**
   - Detect library imports and registrations
   - Identify global component registrations
   - Extract plugin configurations

3. **Scan configuration files**
   - Vuetify: `vuetify.config.ts`, theme customizations
   - Quasar: `quasar.config.js`, framework configuration
   - PrimeVue: `primevue.config.js`, theme settings

4. **Build comprehensive component catalog**
   - **Form Components**: List all inputs, dropdowns, buttons, checkboxes, switches, date pickers, file uploads, filters
   - **Interactive Components**: List tabs, accordions, menus, tooltips, popovers
   - **Data Display**: List tables, lists, cards, chips, badges
   - **Layout Components**: List dialogs, drawers, navigation
   - **Feedback Components**: List alerts, snackbars, progress indicators
   - Document component APIs and props for each
   - Identify styling/theming capabilities
   - Note validation patterns and error handling

### Form Component Priority Mapping

When analyzing component libraries, prioritize mapping of form elements:

**High Priority Form Components**:
1. **Text Inputs**: `v-text-field`, `q-input`, `InputText`, `el-input`, `a-input`
2. **Dropdowns/Selects**: `v-select`, `q-select`, `Dropdown`, `el-select`, `a-select`
3. **Buttons**: `v-btn`, `q-btn`, `Button`, `el-button`, `a-button`
4. **Checkboxes**: `v-checkbox`, `q-checkbox`, `Checkbox`, `el-checkbox`, `a-checkbox`
5. **Radio Buttons**: `v-radio`, `q-radio`, `RadioButton`, `el-radio`, `a-radio`
6. **Switches/Toggles**: `v-switch`, `q-toggle`, `InputSwitch`, `el-switch`, `a-switch`
7. **Date Pickers**: `v-date-picker`, `q-date`, `Calendar`, `el-date-picker`, `a-date-picker`
8. **File Upload**: `v-file-input`, `q-file`, `FileUpload`, `el-upload`, `a-upload`
9. **Filters**: Custom filter components built on top of inputs/selects
10. **Autocomplete**: `v-autocomplete`, `q-select` (with filter), `AutoComplete`, `el-autocomplete`, `a-auto-complete`

**Example Catalog Structure**:
```typescript
const vuetifyFormComponents = {
  textInput: {
    component: 'v-text-field',
    props: ['modelValue', 'label', 'placeholder', 'type', 'rules', 'hint', 'error-messages'],
    variants: ['outlined', 'filled', 'solo', 'underlined'],
    validation: true,
    examples: ['<v-text-field v-model="email" label="Email" type="email" />']
  },
  dropdown: {
    component: 'v-select',
    props: ['modelValue', 'items', 'label', 'multiple', 'chips', 'clearable'],
    variants: ['outlined', 'filled', 'solo'],
    validation: true,
    examples: ['<v-select v-model="country" :items="countries" label="Country" />']
  },
  button: {
    component: 'v-btn',
    props: ['color', 'variant', 'size', 'icon', 'disabled', 'loading'],
    variants: ['flat', 'elevated', 'tonal', 'outlined', 'text', 'plain'],
    formControl: true,
    examples: ['<v-btn color="primary" type="submit">Submit</v-btn>']
  },
  checkbox: {
    component: 'v-checkbox',
    props: ['modelValue', 'label', 'color', 'disabled', 'rules'],
    validation: true,
    examples: ['<v-checkbox v-model="agreed" label="I agree to terms" />']
  },
  filter: {
    component: 'v-text-field',
    props: ['modelValue', 'label', 'prepend-inner-icon', 'clearable'],
    usage: 'search-filter',
    examples: ['<v-text-field v-model="search" label="Search" prepend-inner-icon="mdi-magnify" clearable />']
  }
}
```

### Component Library Mapping

```typescript
interface ComponentLibraryInfo {
  name: 'vuetify' | 'quasar' | 'primevue' | 'element-plus' | 'ant-design-vue' | 'none'
  version: string
  installedComponents: string[]
  availableComponents: ComponentCatalog
  themeConfig: ThemeConfiguration
  globalComponents: string[]
}

interface ComponentCatalog {
  // Form Components (HIGH PRIORITY)
  inputs: ComponentMapping[]           // Text inputs, number inputs, email, password
  textareas: ComponentMapping[]        // Multi-line text inputs
  dropdowns: ComponentMapping[]        // Select dropdowns, autocompletes
  buttons: ComponentMapping[]          // Primary, secondary, icon buttons
  checkboxes: ComponentMapping[]       // Single checkboxes, checkbox groups
  radioButtons: ComponentMapping[]     // Radio button groups
  switches: ComponentMapping[]         // Toggle switches
  sliders: ComponentMapping[]          // Range sliders
  datePickers: ComponentMapping[]      // Date and time pickers
  fileUploads: ComponentMapping[]      // File upload components
  filters: ComponentMapping[]          // Filter components, search filters
  
  // Interactive Components
  tabs: ComponentMapping[]             // Tab navigation
  accordions: ComponentMapping[]       // Collapsible accordions
  tooltips: ComponentMapping[]         // Tooltip overlays
  popovers: ComponentMapping[]         // Popover dialogs
  menus: ComponentMapping[]            // Dropdown menus, context menus
  
  // Data Display Components
  tables: ComponentMapping[]           // Data tables, data grids
  lists: ComponentMapping[]            // Ordered/unordered lists
  cards: ComponentMapping[]            // Card containers
  chips: ComponentMapping[]            // Chip/tag components
  badges: ComponentMapping[]           // Badge indicators
  avatars: ComponentMapping[]          // User avatars
  
  // Layout Components
  dialogs: ComponentMapping[]          // Modal dialogs
  drawers: ComponentMapping[]          // Side drawers
  navigation: ComponentMapping[]       // Nav bars, breadcrumbs
  
  // Feedback Components
  alerts: ComponentMapping[]           // Alert messages
  snackbars: ComponentMapping[]        // Toast notifications
  progressBars: ComponentMapping[]     // Progress indicators
  skeletons: ComponentMapping[]        // Loading skeletons
  spinners: ComponentMapping[]         // Loading spinners
}

interface ComponentMapping {
  figmaType: string           // Type of Figma element
  libraryComponent: string    // Library component name (e.g., 'v-text-field', 'q-input')
  props: PropMapping[]        // Available props
  variants: VariantMapping[]  // Component variants
  examples: string[]          // Usage examples
  formInput: boolean          // Is this a form input component?
  formControl: boolean        // Is this a form control (button, submit)?
  interactive: boolean        // Requires user interaction?
  dataDisplay: boolean        // Displays data/content?
}
```
```

## Existing Component Analysis

### Custom Component Detection
```typescript
function analyzeExistingComponents(): ExistingComponentsMap {
  const components = {
    custom: [],
    patterns: [],
    conventions: {}
  }
  
  // 1. Scan src/components/ directory
  const componentFiles = scanDirectory('src/components')
  
  // 2. Analyze each component
  componentFiles.forEach(file => {
    const analysis = {
      name: extractComponentName(file),
      path: file,
      apiStyle: detectApiStyle(file), // 'options' | 'composition'
      props: extractProps(file),
      emits: extractEmits(file),
      slots: extractSlots(file),
      styling: extractStyling(file),
      dependencies: extractDependencies(file),
      usesLibraryComponents: detectLibraryUsage(file)
    }
    components.custom.push(analysis)
  })
  
  // 3. Identify reusable patterns
  components.patterns = identifyPatterns(components.custom)
  
  // 4. Extract conventions
  components.conventions = {
    namingPattern: detectNamingPattern(components.custom),
    fileStructure: detectFileStructure(components.custom),
    stylingApproach: detectStylingApproach(components.custom),
    testingPattern: detectTestingPattern(components.custom)
  }
  
  return components
}
```

## Integration with Figma Parser

### Component Matching Strategy
When generating components from Figma:

1. **Analyze Figma element thoroughly** (button, input, dropdown, checkbox, filter, etc.)
   - Identify element type and purpose
   - Check for form input patterns (text fields, selects, etc.)
   - Detect interactive controls (buttons, checkboxes, switches)
   - Note validation requirements
   - Extract placeholder text, labels, helper text
   
2. **Check if component library is available**
   - If `useComponentLibrary: 'vuetify'` → Use Vuetify components
   - If `useComponentLibrary: 'none'` → Generate custom component
   
3. **Prioritize form component matching**:
   - **Text Input** in Figma → Map to `v-text-field`, `q-input`, etc.
   - **Dropdown** in Figma → Map to `v-select`, `q-select`, etc.
   - **Button** in Figma → Map to `v-btn`, `q-btn`, etc.
   - **Checkbox** in Figma → Map to `v-checkbox`, `q-checkbox`, etc.
   - **Switch** in Figma → Map to `v-switch`, `q-toggle`, etc.
   - **Date field** in Figma → Map to `v-date-picker`, `q-date`, etc.
   - **Filter/Search** in Figma → Map to input with search icon
   
4. **Look for existing custom component** that matches
   
5. **Select best match**:
   - Priority 1: **Library form component** (for inputs, dropdowns, buttons, checkboxes)
   - Priority 2: Exact match in custom components
   - Priority 3: Library component with matching functionality
   - Priority 4: Generate new custom component following project conventions

### Example Matching Flow
```typescript
function matchComponentForFigmaElement(
  figmaElement: FigmaNode,
  projectContext: ProjectAnalysis
): ComponentMatch {
  
  const elementType = identifyElementType(figmaElement) // 'text-input', 'dropdown', 'button', 'checkbox', etc.
  const isFormComponent = isFormElement(figmaElement) // Check if it's a form input
  
  // PRIORITY 1: Form components from library (if available)
  if (isFormComponent && projectContext.componentLibrary.name !== 'none') {
    const formMatch = findFormComponent(
      elementType,
      projectContext.componentLibrary
    )
    if (formMatch) {
      return {
        type: 'library-form',
        component: formMatch.libraryComponent,
        props: mapFigmaPropsToLibrary(figmaElement, formMatch),
        strategy: 'use-library-form',
        priority: 'high'
      }
    }
  }
  
  // PRIORITY 2: Check for custom component match
  const customMatch = findCustomComponent(elementType, projectContext.customComponents)
  if (customMatch) {
    return {
      type: 'custom',
      component: customMatch,
      strategy: 'reuse-existing',
      priority: 'medium'
    }
  }
  
  // PRIORITY 3: Check for other library component match
  if (projectContext.componentLibrary.name !== 'none') {
    const libraryMatch = findLibraryComponent(
      elementType,
      projectContext.componentLibrary
    )
    if (libraryMatch) {
      return {
        type: 'library',
        component: libraryMatch.libraryComponent,
        props: mapFigmaPropsToLibrary(figmaElement, libraryMatch),
        strategy: 'use-library',
        priority: 'medium'
      }
    }
  }
  
  // PRIORITY 4: Generate new custom component
  return {
    type: 'new-custom',
    component: null,
    strategy: 'generate-new',
    conventions: projectContext.conventions,
    priority: 'low'
  }
}

function isFormElement(figmaElement: FigmaNode): boolean {
  const formTypes = [
    'text-input',
    'number-input', 
    'email-input',
    'password-input',
    'textarea',
    'dropdown',
    'select',
    'autocomplete',
    'checkbox',
    'radio',
    'switch',
    'toggle',
    'slider',
    'date-picker',
    'time-picker',
    'file-upload',
    'color-picker'
  ]
  
  const elementType = identifyElementType(figmaElement)
  return formTypes.includes(elementType)
}

function findFormComponent(
  elementType: string,
  library: ComponentLibraryInfo
): ComponentMapping | null {
  
  // Map Figma element types to library form components
  const formMappings = {
    'text-input': library.availableComponents.inputs,
    'textarea': library.availableComponents.textareas,
    'dropdown': library.availableComponents.dropdowns,
    'select': library.availableComponents.dropdowns,
    'checkbox': library.availableComponents.checkboxes,
    'radio': library.availableComponents.radioButtons,
    'switch': library.availableComponents.switches,
    'slider': library.availableComponents.sliders,
    'date-picker': library.availableComponents.datePickers,
    'file-upload': library.availableComponents.fileUploads,
    'filter': library.availableComponents.filters
  }
  
  const matchingComponents = formMappings[elementType]
  return matchingComponents?.[0] || null
}
```

## Output

Returns comprehensive project context:
```typescript
interface ProjectAnalysis {
  structure: {
    type: 'vue3-vite' | 'vue3-cli' | 'nuxt3'
    directories: string[]
    conventions: NamingConventions
  }
  componentLibrary: ComponentLibraryInfo
  customComponents: ExistingComponent[]
  designSystem: {
    tokens: DesignTokens
    themeFiles: string[]
    cssVariables: CSSVariables
  }
  stateManagement: {
    library: 'pinia' | 'vuex' | 'none'
    stores: StoreInfo[]
  }
  routing: {
    library: 'vue-router'
    routes: RouteInfo[]
    patterns: RoutingPatterns
  }
  testingFramework: {
    name: 'vitest' | 'jest' | 'none'
    patterns: TestPatterns
  }
}
```

## Error Handling
- Missing component library configuration → Use custom generation
- Incompatible library versions → Warn and fallback to custom
- No existing components → Use pure Figma-to-code generation
- Missing design tokens → Extract from Figma only
