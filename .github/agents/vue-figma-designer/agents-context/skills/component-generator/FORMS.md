# Component Generator Forms

## Component Configuration Form

### Basic Component Information
```yaml
componentName:
  type: string
  required: true
  validation: "Must be PascalCase"
  placeholder: "ProductCard"

componentType:
  type: select
  required: true
  options: ["presentation", "container", "form", "layout"]
  default: "presentation"

description:
  type: textarea
  required: false
  placeholder: "Brief description of the component's purpose"
```

### Props Configuration
```yaml
props:
  type: array
  items:
    name:
      type: string
      required: true
      validation: "Must be camelCase"
    
    type:
      type: select
      required: true
      options: ["String", "Number", "Boolean", "Object", "Array", "Function"]
    
    required:
      type: boolean
      default: false
    
    default:
      type: dynamic
      description: "Default value (based on type)"
    
    validator:
      type: string
      required: false
      description: "Custom validation function"
    
    description:
      type: string
      required: false
```

### Events Configuration
```yaml
events:
  type: array
  items:
    name:
      type: string
      required: true
      validation: "Must be kebab-case"
      placeholder: "item-selected"
    
    payload:
      type: object
      required: false
      description: "Event payload type definition"
    
    description:
      type: string
      required: false
```

### Slots Configuration
```yaml
slots:
  type: array
  items:
    name:
      type: string
      required: true
      default: "default"
    
    scoped:
      type: boolean
      default: false
    
    scopedProps:
      type: object
      required: false
      description: "Props passed to scoped slot"
    
    fallback:
      type: string
      required: false
      description: "Fallback content"
```

## Styling Configuration

### Design Integration
```yaml
useDesignTokens:
  type: boolean
  default: true
  description: "Integrate with design token system"

responsiveBreakpoints:
  type: multiselect
  options: ["mobile", "tablet", "desktop", "large"]
  default: ["mobile", "desktop"]

animations:
  type: boolean
  default: false
  description: "Include animation styles"

themes:
  type: multiselect
  options: ["light", "dark", "high-contrast"]
  default: ["light"]
```

### Performance Options
```yaml
lazyLoad:
  type: boolean
  default: false
  description: "Enable lazy loading for images"

virtualScroll:
  type: boolean
  default: false
  description: "Enable virtual scrolling for lists"

memoization:
  type: boolean
  default: false
  description: "Add computed property memoization"
```

## Accessibility Configuration

### ARIA Support
```yaml
ariaLabels:
  type: boolean
  default: true
  description: "Generate appropriate ARIA labels"

keyboardNavigation:
  type: boolean
  default: false
  description: "Add keyboard navigation support"

screenReader:
  type: boolean
  default: true
  description: "Optimize for screen readers"

colorContrast:
  type: boolean
  default: true
  description: "Ensure WCAG color contrast compliance"
```