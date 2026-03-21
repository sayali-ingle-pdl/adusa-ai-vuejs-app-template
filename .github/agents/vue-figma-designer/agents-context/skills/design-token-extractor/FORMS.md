# Design Token Extractor Forms

## Token Extraction Configuration

### Output Formats
```yaml
outputFormats:
  type: multiselect
  required: true
  options: ["scss", "css", "typescript", "json"]
  default: ["scss", "typescript"]
  description: "Formats to generate for design tokens"

namingConvention:
  type: select
  required: true
  options: ["kebab-case", "camelCase", "snake_case"]
  default: "kebab-case"
  description: "Naming convention for token variables"

tokenPrefix:
  type: string
  required: false
  placeholder: "ds" 
  description: "Prefix for all token variables (e.g., ds-color-primary)"
```

### Token Categories
```yaml
includeColors:
  type: boolean
  default: true
  description: "Extract color tokens"

includeTypography:
  type: boolean
  default: true
  description: "Extract typography tokens"

includeSpacing:
  type: boolean
  default: true
  description: "Extract spacing tokens"

includeShadows:
  type: boolean
  default: true
  description: "Extract shadow tokens"

includeBorders:
  type: boolean
  default: true
  description: "Extract border and radius tokens"

includeBreakpoints:
  type: boolean
  default: true
  description: "Extract responsive breakpoint tokens"
```

### Color Configuration
```yaml
colorFormats:
  type: multiselect
  options: ["hex", "rgb", "hsl", "oklch"]
  default: ["hex", "rgb"]
  description: "Color formats to include"

generateColorPalettes:
  type: boolean
  default: true
  description: "Generate color palette variations (50, 100, 200, etc.)"

semanticColors:
  type: boolean
  default: true
  description: "Generate semantic color tokens (success, warning, error)"

darkModeSupport:
  type: boolean
  default: false
  description: "Generate dark mode color variants"
```

### Typography Configuration
```yaml
fontStack:
  type: select
  options: ["web-safe", "google-fonts", "custom", "system"]
  default: "system"
  description: "Font stack approach"

includeWebfonts:
  type: boolean
  default: false
  description: "Generate web font loading configurations"

typescaleRatio:
  type: select
  options: ["1.125", "1.2", "1.25", "1.333", "1.414", "1.5", "1.618"]
  default: "1.25"
  description: "Type scale ratio for font size generation"

generateUtilities:
  type: boolean
  default: true
  description: "Generate typography utility classes"
```