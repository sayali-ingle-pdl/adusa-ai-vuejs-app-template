# Figma Parser Forms

# Figma Parser Forms

## Configuration File Input (Recommended)

The Figma parser reads configuration from `config/app-figma-code-generator-config.json`:

```json
{
  "$schema": "./app-figma-code-generator-config.schema.json",
  "figmaUrls": [
    "https://www.figma.com/file/ABC123/Component-Desktop?node-id=1:2",
    "https://www.figma.com/file/ABC123/Component-Mobile?node-id=2:5"
  ],
  "figmaToken": "figd_12345abcdef67890ghijklmnop",
  "name": "HomePage",
  "type": "component",
  "useComponentLibrary": "none"
}
```

### Configuration Schema
See `config/app-figma-code-generator-config.schema.json` for the complete JSON Schema definition.

## Direct Input Form (Alternative)

### Required Information
- **Figma URLs**: Array of Figma file URLs (hierarchical: primary + variants)
- **Figma Access Token**: Personal access token for API authentication
- **Component Name**: Name for the generated component/view (PascalCase)
- **Type**: 'component' or 'view'

### Form Fields

#### Basic Configuration
```yaml
figmaUrls:
  type: array
  items:
    type: url
  required: true
  validation: "Must be valid Figma file URLs"
  placeholder: ["https://www.figma.com/file/..."]
  description: "First URL is primary/high-level, subsequent URLs are variants/rows"

accessToken:
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