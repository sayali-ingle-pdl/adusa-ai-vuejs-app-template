# Figma Parser Forms

## Figma URL Input Form

### Required Information
- **Figma File URL**: Complete Figma file URL (e.g., https://www.figma.com/file/abc123/MyDesign)
- **Figma Access Token**: Personal access token for API authentication
- **Page Selection**: Specific pages to parse (optional, defaults to all)
- **Component Filter**: Filter by component names or patterns (optional)

### Form Fields

#### Basic Configuration
```yaml
figmaUrl:
  type: url
  required: true
  validation: "Must be a valid Figma file URL"
  placeholder: "https://www.figma.com/file/..."

accessToken:
  type: password
  required: true
  description: "Generate from Figma Account Settings > Personal Access Tokens"

pageNames:
  type: array
  required: false
  description: "Leave empty to parse all pages"
  placeholder: "['Design System', 'Components']"
```

#### Advanced Options
```yaml
includeAssets:
  type: boolean
  default: true
  description: "Download and optimize image assets"

extractTokens:
  type: boolean
  default: true
  description: "Extract design tokens from styles"

componentFilter:
  type: string
  required: false
  description: "Regex pattern to filter components"
  placeholder: "Button|Card|Modal"

optimizeImages:
  type: boolean
  default: true
  description: "Optimize downloaded images"

maxImageSize:
  type: number
  default: 1920
  description: "Maximum image width/height in pixels"
```

## Output Configuration

### Export Options
```yaml
outputFormat:
  type: select
  options: ["vue-specs", "design-tokens", "full-analysis"]
  default: "full-analysis"

tokenFormats:
  type: multiselect
  options: ["scss", "css", "typescript", "json"]
  default: ["scss", "typescript"]

assetFormats:
  type: multiselect
  options: ["svg", "png", "webp"]
  default: ["svg", "webp"]
```