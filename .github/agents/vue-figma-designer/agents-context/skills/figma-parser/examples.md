# Figma Parser Examples

## Basic Usage

### Parse Complete Figma File
```typescript
const result = await parseFigmaFile({
  figmaUrl: 'https://www.figma.com/file/abc123/MyDesignSystem',
  accessToken: 'figd_xyz789',
  includeAssets: true,
  extractTokens: true
})

// Result structure
{
  components: ComponentSpec[],
  designTokens: DesignTokens,
  assets: AssetMetadata[],
  metadata: FileMetadata
}
```

### Filter Specific Components
```typescript
const result = await parseFigmaFile({
  figmaUrl: 'https://www.figma.com/file/abc123/Components',
  accessToken: 'figd_xyz789',
  componentFilter: 'Button|Card|Input',
  pageNames: ['Components', 'Patterns']
})

console.log(result.components.length) // Only matching components
```

## Component Extraction Examples

### Button Component Parsing
```typescript
// Figma component with variants
const buttonSpec = {
  name: 'Button',
  type: 'component',
  variants: [
    { props: { variant: 'primary', size: 'medium' } },
    { props: { variant: 'secondary', size: 'small' } },
    { props: { variant: 'danger', size: 'large' } }
  ],
  properties: {
    variant: { type: 'string', values: ['primary', 'secondary', 'danger'] },
    size: { type: 'string', values: ['small', 'medium', 'large'] },
    disabled: { type: 'boolean', default: false }
  },
  styling: {
    borderRadius: '4px',
    padding: '8px 16px',
    fontWeight: '600'
  }
}
```

### Card Component with Layout
```typescript
const cardSpec = {
  name: 'ProductCard',
  type: 'component',
  layout: {
    type: 'flex',
    direction: 'column',
    gap: '12px',
    padding: '16px'
  },
  children: [
    { name: 'image', type: 'image', constraints: { width: '100%', height: '200px' } },
    { name: 'title', type: 'text', styling: { fontSize: '18px', fontWeight: '600' } },
    { name: 'description', type: 'text', styling: { fontSize: '14px', color: '#666' } },
    { name: 'button', type: 'component-instance', component: 'Button' }
  ]
}
```

## Design Token Extraction

### Color Tokens
```typescript
const colorTokens = {
  colors: {
    primary: {
      50: '#eff6ff',
      500: '#3b82f6',
      900: '#1e3a8a'
    },
    semantic: {
      success: '#10b981',
      warning: '#f59e0b',
      error: '#ef4444'
    }
  }
}
```

### Typography Tokens
```typescript
const typographyTokens = {
  fonts: {
    sans: ['Inter', 'system-ui', 'sans-serif'],
    mono: ['Fira Code', 'monospace']
  },
  fontSizes: {
    xs: '12px',
    sm: '14px',
    base: '16px',
    lg: '18px',
    xl: '20px'
  },
  fontWeights: {
    normal: '400',
    medium: '500',
    semibold: '600',
    bold: '700'
  }
}
```

### Spacing Tokens
```typescript
const spacingTokens = {
  spacing: {
    0: '0px',
    1: '4px',
    2: '8px',
    3: '12px',
    4: '16px',
    6: '24px',
    8: '32px',
    12: '48px',
    16: '64px'
  }
}
```

## Asset Processing

### Image Asset Example
```typescript
const assets = [
  {
    id: 'img_hero_banner',
    name: 'hero-banner.png',
    type: 'image',
    originalUrl: 'https://figma-alpha-api.s3.us-west-2.amazonaws.com/...',
    optimizedUrls: {
      webp: '/assets/hero-banner.webp',
      png: '/assets/hero-banner.png'
    },
    dimensions: { width: 1200, height: 600 },
    fileSize: 245760
  }
]
```

### SVG Icon Processing
```typescript
const iconAssets = [
  {
    id: 'icon_chevron_right',
    name: 'chevron-right',
    type: 'svg',
    svgContent: '<svg viewBox="0 0 24 24">...</svg>',
    optimized: true,
    usage: ['Button', 'NavigationItem']
  }
]
```

## Error Handling Examples

### Network Error Recovery
```typescript
try {
  const result = await parseFigmaFile(config)
} catch (error) {
  if (error.code === 'RATE_LIMITED') {
    console.log(`Rate limited. Retry after: ${error.retryAfter}`)
    // Implement exponential backoff
  } else if (error.code === 'NETWORK_ERROR') {
    console.log('Network error, retrying...')
    // Implement retry logic
  } else if (error.code === 'INVALID_TOKEN') {
    console.error('Invalid Figma access token')
    // Prompt user for new token
  }
}
```

### Partial Success Handling
```typescript
const result = await parseFigmaFile(config)

if (result.warnings.length > 0) {
  console.warn('Parsing completed with warnings:')
  result.warnings.forEach(warning => {
    console.warn(`- ${warning.message} (${warning.component})`)
  })
}

if (result.errors.length > 0) {
  console.error('Some components failed to parse:')
  result.errors.forEach(error => {
    console.error(`- ${error.message} (${error.component})`)
  })
}
```