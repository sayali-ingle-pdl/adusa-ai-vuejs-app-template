# Figma API Reference

## Authentication
```typescript
const FIGMA_API_BASE = 'https://api.figma.com/v1'
const headers = {
  'X-Figma-Token': accessToken,
  'Content-Type': 'application/json'
}
```

## Core API Endpoints

### Get File
```typescript
GET /files/{key}
// Returns file metadata, document structure, and components
// Includes: layout, styles, colors, typography, effects, constraints
```

### Get File Nodes
```typescript
GET /files/{key}/nodes?ids={nodeIds}
// Returns specific nodes within a file
// Includes all style properties, layout constraints, and fill/stroke data
```

### Get Images
```typescript
GET /images/{key}?ids={nodeIds}&format={format}
// Returns download URLs for node images
// Supported formats: PNG, JPG, SVG, PDF
// Parameters: scale (1x, 2x, 3x, 4x), format, svg_include_id, svg_simplify_stroke
```

### Get Image Fills
```typescript
GET /images/{key}
// Returns download URLs for images used in fills
```

### Get Styles
```typescript
GET /files/{key}/styles
// Returns all styles defined in the file
// Includes: color styles, text styles, effect styles, grid styles
```

### Get Comments
```typescript
GET /files/{key}/comments
// Returns comments on the file
```

## Data Structures

### File Response
```typescript
interface FigmaFile {
  document: DocumentNode
  components: Record<string, Component>
  styles: Record<string, Style>
  schemaVersion: number
  name: string
  thumbnailUrl: string
  version: string
}
```

### Node Structure
```typescript
interface Node {
  id: string
  name: string
  type: NodeType
  visible?: boolean
  children?: Node[]
  
  // Layout properties
  absoluteBoundingBox?: Rectangle
  constraints?: LayoutConstraint
  layoutMode?: 'NONE' | 'HORIZONTAL' | 'VERTICAL'
  layoutAlign?: 'MIN' | 'CENTER' | 'MAX' | 'STRETCH'
  layoutGrow?: number
  primaryAxisSizingMode?: 'FIXED' | 'AUTO'
  counterAxisSizingMode?: 'FIXED' | 'AUTO'
  paddingLeft?: number
  paddingRight?: number
  paddingTop?: number
  paddingBottom?: number
  itemSpacing?: number
  
  // Style properties
  fills?: Paint[]
  strokes?: Paint[]
  strokeWeight?: number
  strokeAlign?: 'INSIDE' | 'OUTSIDE' | 'CENTER'
  cornerRadius?: number
  effects?: Effect[]
  opacity?: number
  blendMode?: BlendMode
  
  // Typography (for text nodes)
  characters?: string
  style?: TypeStyle
  characterStyleOverrides?: number[]
  styleOverrideTable?: Record<number, TypeStyle>
}

// Node types that can be extracted
type NodeType = 
  | 'DOCUMENT'
  | 'CANVAS'
  | 'FRAME'          // Containers, sections, headers
  | 'GROUP'          // Grouped elements
  | 'VECTOR'         // Icons, shapes
  | 'BOOLEAN_OPERATION'
  | 'STAR'
  | 'LINE'
  | 'ELLIPSE'
  | 'REGULAR_POLYGON'
  | 'RECTANGLE'
  | 'TEXT'           // ✅ Text content (labels, headings, paragraphs)
  | 'SLICE'
  | 'COMPONENT'      // ✅ Reusable components (buttons, modals, dialogs)
  | 'COMPONENT_SET'  // ✅ Component variants (button states, alert types)
  | 'INSTANCE'       // ✅ Component instances used in designs

interface Paint {
  type: 'SOLID' | 'GRADIENT_LINEAR' | 'GRADIENT_RADIAL' | 'IMAGE'
  color?: Color
  opacity?: number
  gradientStops?: ColorStop[]
  imageRef?: string
}

interface Color {
  r: number  // 0-1
  g: number  // 0-1
  b: number  // 0-1
  a: number  // 0-1
}

interface Effect {
  type: 'DROP_SHADOW' | 'INNER_SHADOW' | 'LAYER_BLUR' | 'BACKGROUND_BLUR'
  visible: boolean
  radius: number
  color?: Color
  offset?: Vector
  spread?: number
}

interface TypeStyle {
  fontFamily: string
  fontPostScriptName: string
  fontWeight: number
  fontSize: number
  letterSpacing: number
  lineHeightPx: number
  lineHeightPercent: number
  textAlignHorizontal: 'LEFT' | 'CENTER' | 'RIGHT' | 'JUSTIFIED'
  textAlignVertical: 'TOP' | 'CENTER' | 'BOTTOM'
}
```

## Accessible UI Elements

The Figma API provides full access to all UI elements:

### ✅ Text Content
- **TEXT nodes**: Access to all text content via `characters` property
- **Headings**: Text nodes with specific styles (h1, h2, h3, etc.)
- **Paragraphs**: Body text content
- **Labels**: Button labels, form labels, input placeholders
- **Lists**: Text in list items
- **Error messages**: Validation text, error states

### ✅ Component Instances
- **Buttons**: Icon buttons, text buttons, FABs
  - Access to button text, icons, states (default, hover, active, disabled)
- **Alerts**: Alert components with icons, messages, action buttons
- **Modals/Dialogs**: 
  - Dialog titles (TEXT nodes)
  - Dialog content/body (TEXT nodes)
  - Dialog actions/buttons (COMPONENT instances)
  - Close buttons/icons
- **Form Controls**:
  - Input fields with labels and placeholders
  - Checkboxes and radio buttons
  - Dropdowns/selects
  - Toggles/switches

### ✅ Layout Structures
- **Sections**: FRAME nodes representing sections
- **Headers**: FRAME nodes for page/section headers
- **Rows**: FRAME nodes with horizontal layout (`layoutMode: 'HORIZONTAL'`)
- **Columns**: FRAME nodes with vertical layout (`layoutMode: 'VERTICAL'`)
- **Grids**: Layout grids and auto-layout configurations
- **Navigation**: Nav bars, sidebars, menus

### ✅ Icons & Graphics
- **Vector Icons**: VECTOR nodes (can be exported as SVG)
- **Icon Buttons**: COMPONENT nodes containing VECTOR children
- **Images**: IMAGE fills in nodes
- **Logos**: GROUP or VECTOR nodes

### ✅ Interactive Elements
- **Buttons**: Full component structure with text and icons
- **Links**: Text nodes with specific styling
- **Tabs**: Tab components with active states
- **Accordions**: Expandable sections
- **Cards**: Card components with headers, content, actions
```

### Component Definition
```typescript
interface Component {
  key: string
  name: string
  description: string
  componentSetId?: string
  documentationLinks: DocumentationLink[]
}
```

### Style Definition
```typescript
interface Style {
  key: string
  name: string
  styleType: StyleType
  description: string
}

type StyleType = 'FILL' | 'TEXT' | 'EFFECT' | 'GRID'
```

## Rate Limiting
- 1000 requests per hour per access token
- Implements exponential backoff on rate limit hits
- Batches requests when possible

## Error Codes
- `400` - Bad Request (invalid parameters)
- `403` - Forbidden (invalid token or no access)
- `404` - Not Found (file doesn't exist)
- `429` - Too Many Requests (rate limited)
- `500` - Internal Server Error

## Best Practices
1. Cache responses when possible
2. Use batch requests for multiple nodes
3. Implement proper error handling and retries
4. Validate tokens before making requests
5. Monitor rate limit headers