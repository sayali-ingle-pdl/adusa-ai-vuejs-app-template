# Figma Parser Skill

## Overview
Extracts design specifications, components, and assets from Figma URLs using the Figma REST API. Processes design tokens, component hierarchies, and visual assets for Vue component generation.

## Capabilities
- Parse Figma files and extract component trees
- **Thoroughly analyze all UI elements** including form components
- **Identify form inputs**: text fields, number inputs, email, password, textareas
- **Identify dropdowns**: selects, autocompletes, multi-selects
- **Identify buttons**: primary, secondary, icon buttons, submit buttons
- **Identify checkboxes**: single checkboxes, checkbox groups
- **Identify filters**: search filters, data filters, advanced filters
- **Identify radio buttons**: radio groups, button groups
- **Identify switches/toggles**: on/off switches
- **Identify date/time pickers**: date pickers, time pickers, date ranges
- **Identify file uploads**: single file, multiple files, drag-and-drop
- Extract design tokens (colors, typography, spacing, shadows, borders, effects)
- **Download and optimize image assets** (PNG, SVG, WebP formats)
- **Extract layout information** (flexbox, grid, constraints, positioning)
- **Extract style properties** (fills, strokes, effects, shadows, borders)
- **Extract color palettes** from Figma styles and local styles
- Map component variants and properties
- Analyze layout structures and constraints
- Generate component specifications for Vue generation
- **Download assets to project** (`public/assets/` or `src/assets/`)

### Asset Download Features
- **Images**: Downloads image nodes and exports as PNG/WebP/SVG
- **Icons**: Extracts SVG icons and optimizes them
- **Fonts**: Identifies font families used in designs
- **Colors**: Extracts all color values from fills and strokes
- **Effects**: Captures shadows, blurs, and other effects
- **Optimization**: Automatically optimizes downloaded assets (compression, format conversion)

### Form Component Analysis
- **Input Detection**: Identifies text inputs, number inputs, email fields, password fields
- **Dropdown Detection**: Identifies select menus, autocomplete fields, multi-select dropdowns
- **Button Detection**: Primary buttons, secondary buttons, icon buttons, submit/cancel buttons
- **Checkbox Detection**: Single checkboxes, checkbox groups with labels
- **Filter Detection**: Search bars, filter dropdowns, advanced filter panels
- **Radio Detection**: Radio button groups with options
- **Switch Detection**: Toggle switches, on/off controls
- **Upload Detection**: File upload components, drag-and-drop zones
- **Validation Detection**: Error states, validation messages, required field indicators
- **Label/Placeholder Extraction**: Field labels, placeholder text, helper text, hints

## Usage
This skill is automatically triggered when Figma URL(s) are provided. It handles authentication, rate limiting, and data transformation.

### Multi-URL Processing Strategy
When multiple Figma URLs are provided (via `figmaUrls` array), they are processed in a hierarchical order:

1. **First URL (Primary/High-Level)**: The first URL in the array is treated as the primary high-level file. It provides the base component structure and specifications.

2. **Additional URLs (Variants/Details)**: Subsequent URLs are processed to extract detailed information:
   - Each URL is parsed as a "row" or variant
   - Within each row URL, individual components are extracted and processed
   - Components from subsequent URLs enhance or provide variants to the primary structure

**Processing Flow**:
```
Primary URL → Extract base component structure
  ↓
Second URL (Row 1) → Parse as variant/state
  ↓ For each component in Row 1:
    → Component A details
    → Component B details
    → Component C details
  ↓
Third URL (Row 2) → Parse as variant/state
  ↓ For each component in Row 2:
    → Component D details
    → Component E details
...
```

This hierarchical approach allows capturing:
- Desktop/mobile variants
- Different component states (default, hover, active, disabled)
- Responsive breakpoint designs
- Theme variations (light/dark mode)

### Hierarchical Layer Parsing (Single or Multiple URLs)

**IMPORTANT**: Whether a single URL or multiple URLs are provided, the parser ALWAYS traverses the complete component hierarchy layer by layer:

```
File/URL Level
  ↓
Modal/Container Level
  ↓
Title/Header Level
  ↓
Section Level
  ↓
Row Level (within sections)
  ↓
Additional Headers Level (within rows)
  ↓
Nested Rows Level (within headers)
  ↓
Individual Components Level (buttons, icons, text)
```

**Layer-by-Layer Parsing Strategy**:

1. **Root Level**: Parse the main frame/component
2. **Container Level**: Extract modals, dialogs, cards
3. **Header Level**: Extract titles, section headers
4. **Section Level**: Extract content sections
5. **Row Level**: Extract horizontal/vertical rows within sections
6. **Nested Headers**: Extract sub-headers within rows
7. **Nested Rows**: Extract nested row structures
8. **Leaf Elements**: Extract final components (text, icons, buttons)

**Example Hierarchy**:
```
HomePage (URL)
├── Modal (Layer 1: Container)
│   ├── Modal Title (Layer 2: Title)
│   ├── Modal Header (Layer 2: Header)
│   │   ├── Header Text (Layer 3: Text)
│   │   └── Close Button (Layer 3: Component)
│   ├── Modal Content Section (Layer 2: Section)
│   │   ├── Section Header (Layer 3: Header)
│   │   │   └── Section Title (Layer 4: Text)
│   │   ├── Row 1 (Layer 3: Row)
│   │   │   ├── Nested Header (Layer 4: Header)
│   │   │   │   └── Sub-title (Layer 5: Text)
│   │   │   └── Nested Row (Layer 4: Row)
│   │   │       ├── Icon (Layer 5: Icon)
│   │   │       └── Label (Layer 5: Text)
│   │   ├── Row 2 (Layer 3: Row)
│   │   │   ├── Nested Header (Layer 4: Header)
│   │   │   └── Nested Row (Layer 4: Row)
│   │   └── Row 3 (Layer 3: Row)
│   └── Modal Actions Section (Layer 2: Section)
│       └── Action Row (Layer 3: Row)
│           ├── Cancel Button (Layer 4: Component)
│           └── Confirm Button (Layer 4: Component)
```

This ensures complete extraction of all nested structures regardless of depth.

## Key Functions
- `parseFigmaFile()` - Main entry point for parsing Figma files
- `extractDesignTokens()` - Processes design tokens from styles
- `processComponentHierarchy()` - **Maps complete component structure layer by layer**
- `traverseNodeTree()` - **Recursively traverses all nested nodes (modal → title → header → section → rows → nested headers → nested rows → components)**
- `extractLayersByDepth()` - **Groups nodes by hierarchical depth for structured processing**
- `identifyFormComponents()` - **Detects and classifies form inputs, dropdowns, buttons, checkboxes, filters**
- `extractFormProperties()` - **Extracts labels, placeholders, validation rules, field types**
- `detectInteractiveElements()` - **Identifies buttons, checkboxes, switches, radio buttons**
- `extractDropdownOptions()` - **Extracts options from select/dropdown components**
- `analyzeFilterComponents()` - **Identifies search filters, data filters, advanced filters**
- `optimizeAssets()` - Downloads and processes images
- `generateComponentSpecs()` - Creates Vue component specifications

## Hierarchical Parsing Algorithm

### Layer Detection and Processing
```typescript
function parseComponentHierarchy(figmaNode: Node): ComponentHierarchy {
  const hierarchy = {
    depth: 0,
    layers: []
  }
  
  // Layer 1: Root container (Modal, Dialog, Card, etc.)
  const rootContainer = identifyContainer(figmaNode)
  hierarchy.layers.push({ depth: 1, type: 'container', node: rootContainer })
  
  // Layer 2: Direct children (Title, Header, Sections)
  rootContainer.children.forEach(child => {
    if (isTitle(child)) {
      hierarchy.layers.push({ depth: 2, type: 'title', node: child })
    } else if (isHeader(child)) {
      hierarchy.layers.push({ depth: 2, type: 'header', node: child })
      // Traverse header children
      traverseHeaderChildren(child, 3, hierarchy)
    } else if (isSection(child)) {
      hierarchy.layers.push({ depth: 2, type: 'section', node: child })
      // Layer 3+: Section contents (rows, nested headers, etc.)
      traverseSectionChildren(child, 3, hierarchy)
    }
  })
  
  return hierarchy
}

function traverseSectionChildren(section: Node, depth: number, hierarchy: ComponentHierarchy) {
  section.children.forEach(child => {
    if (isHeader(child)) {
      // Section header at depth
      hierarchy.layers.push({ depth, type: 'header', node: child })
      traverseHeaderChildren(child, depth + 1, hierarchy)
    } else if (isRow(child)) {
      // Row at depth
      hierarchy.layers.push({ depth, type: 'row', node: child })
      // Check for nested headers and rows within this row
      traverseRowChildren(child, depth + 1, hierarchy)
    }
  })
}

function traverseRowChildren(row: Node, depth: number, hierarchy: ComponentHierarchy) {
  row.children.forEach(child => {
    if (isHeader(child)) {
      // Nested header within row
      hierarchy.layers.push({ depth, type: 'nested-header', node: child })
      traverseHeaderChildren(child, depth + 1, hierarchy)
    } else if (isRow(child)) {
      // Nested row within row
      hierarchy.layers.push({ depth, type: 'nested-row', node: child })
      traverseRowChildren(child, depth + 1, hierarchy)
    } else if (isComponent(child)) {
      // Leaf component (button, icon, text, etc.)
      hierarchy.layers.push({ depth, type: 'component', node: child })
    }
  })
}
```

### Processing Order
1. **Parse root container** (Modal, Dialog, Page, etc.)
2. **Extract title** if present
3. **Extract headers** (main headers, section headers)
4. **Process sections** in order
5. **Within each section**: Extract rows sequentially
6. **Within each row**: 
   - Check for nested headers
   - Extract nested headers
   - Check for nested rows
   - Extract nested rows recursively
   - **Identify form components** (inputs, dropdowns, buttons, checkboxes, filters)
   - **Extract form properties** (labels, placeholders, types, validation)
7. **Classify interactive elements**:
   - **Form Inputs**: Text fields, textareas, number inputs, email, password
   - **Dropdowns**: Selects, autocompletes, multi-selects with options
   - **Buttons**: Primary, secondary, icon buttons, submit/cancel
   - **Checkboxes**: Single checkboxes, checkbox groups
   - **Radio Buttons**: Radio groups with options
   - **Switches**: Toggle switches, on/off controls
   - **Date Pickers**: Date and time selection components
   - **File Uploads**: File input components
   - **Filters**: Search filters, data filters, advanced filters
8. **Extract leaf components** (icons, text, images, badges)
9. **Build component tree** maintaining hierarchical structure with component classifications

## Output
Returns structured data including:
- Component hierarchy and specifications
- Design tokens in multiple formats (SCSS, CSS custom properties, TypeScript)
- **Optimized assets downloaded to project directories**:
  - Images: `public/assets/images/`
  - Icons: `src/assets/icons/`
  - Fonts: `public/assets/fonts/` (if custom fonts detected)
- **Layout information**:
  - Flexbox/Grid properties
  - Positioning and constraints
  - Spacing and padding values
  - Responsive breakpoints
- **Styling information**:
  - Color values (fills, strokes, backgrounds)
  - Typography (font families, sizes, weights, line heights)
  - Effects (shadows, blurs, opacity)
  - Borders and border-radius
  - Transitions and animations (if specified)
- Interaction and animation data
- Component state variations

## Error Handling
- Rate limiting with exponential backoff
- Network error recovery
- Invalid URL validation
- Missing component graceful degradation