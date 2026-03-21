# Figma Parser Examples

## Basic Usage

### Parse Single Figma File
```typescript
const result = await parseFigmaFile({
  figmaUrls: ['https://www.figma.com/file/abc123/MyDesignSystem'],
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

### Parse Multiple Figma URLs (Hierarchical Processing)
```typescript
const result = await parseFigmaFile({
  figmaUrls: [
    'https://www.figma.com/file/abc123/HomePage-Desktop?node-id=1:2',  // Primary/high-level
    'https://www.figma.com/file/abc123/HomePage-Mobile?node-id=2:5',   // Row 1: Mobile variant
    'https://www.figma.com/file/abc123/HomePage-Tablet?node-id=3:8'    // Row 2: Tablet variant
  ],
  accessToken: 'figd_xyz789',
  componentName: 'HomePage',
  includeAssets: true,
  extractTokens: true
})

// Processing order:
// 1. Parse primary URL (HomePage-Desktop) → Base component structure
// 2. Parse row URL (HomePage-Mobile) → Extract each component in this variant
// 3. Parse row URL (HomePage-Tablet) → Extract each component in this variant
// Result merges all variants into comprehensive component spec

{
  component: {
    name: 'HomePage',
    baseStructure: { /* from primary URL */ },
    variants: {
      desktop: { /* extracted from URL 1 */ },
      mobile: { /* extracted from URL 2 with all sub-components */ },
      tablet: { /* extracted from URL 3 with all sub-components */ }
    },
    components: [
      { name: 'Header', variants: ['desktop', 'mobile', 'tablet'] },
      { name: 'Hero', variants: ['desktop', 'mobile', 'tablet'] },
      { name: 'Footer', variants: ['desktop', 'mobile', 'tablet'] }
    ]
  },
  designTokens: DesignTokens,
  assets: AssetMetadata[],
  metadata: FileMetadata
}
```

### Parse Component States with Multiple URLs
```typescript
const result = await parseFigmaFile({
  figmaUrls: [
    'https://www.figma.com/file/xyz789/Button-Default?node-id=5:10',  // Primary state
    'https://www.figma.com/file/xyz789/Button-States?node-id=6:15',    // Row with all states
    'https://www.figma.com/file/xyz789/Button-Variants?node-id=7:20'   // Row with size variants
  ],
  accessToken: 'figd_xyz789',
  componentName: 'Button'
})

// Processing extracts:
// - Base button structure from first URL
// - Each state component (hover, active, disabled) from second URL
// - Each size variant component (small, medium, large) from third URL
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
    downloadedPath: 'public/assets/images/hero-banner.png',
    optimizedUrls: {
      webp: 'public/assets/images/hero-banner.webp',
      png: 'public/assets/images/hero-banner.png',
      '2x': 'public/assets/images/hero-banner@2x.png'
    },
    dimensions: { width: 1200, height: 600 },
    fileSize: 245760,
    optimizedSize: 156320
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
    downloadedPath: 'src/assets/icons/chevron-right.svg',
    optimized: true,
    usage: ['Button', 'NavigationItem'],
    size: '24x24'
  }
]
```

## UI Element Extraction Examples

### Text Content Extraction
```typescript
// Extract all text from a design
const textNodes = {
  heading: {
    type: 'TEXT',
    characters: 'Welcome to Our App',
    style: {
      fontFamily: 'Inter',
      fontSize: 32,
      fontWeight: 700,
      textAlignHorizontal: 'LEFT'
    }
  },
  paragraph: {
    type: 'TEXT',
    characters: 'This is a description of the feature that helps users understand what they can do.',
    style: {
      fontFamily: 'Inter',
      fontSize: 16,
      fontWeight: 400,
      lineHeightPx: 24
    }
  },
  buttonLabel: {
    type: 'TEXT',
    characters: 'Get Started',
    style: {
      fontFamily: 'Inter',
      fontSize: 14,
      fontWeight: 600
    }
  }
}
```

### Alert Component Extraction
```typescript
const alertComponent = {
  name: 'Alert-Error',
  type: 'COMPONENT',
  children: [
    {
      name: 'Icon',
      type: 'VECTOR',
      // Icon SVG data
      exportedSvg: '<svg>...</svg>'
    },
    {
      name: 'Title',
      type: 'TEXT',
      characters: 'Error',
      style: { fontSize: 14, fontWeight: 600, color: '#ef4444' }
    },
    {
      name: 'Message',
      type: 'TEXT',
      characters: 'Something went wrong. Please try again.',
      style: { fontSize: 14, fontWeight: 400, color: '#991b1b' }
    },
    {
      name: 'Close Button',
      type: 'INSTANCE',
      componentId: 'icon-button-component',
      children: [
        {
          name: 'Close Icon',
          type: 'VECTOR',
          exportedSvg: '<svg>...</svg>'
        }
      ]
    }
  ],
  // Generated Vue component structure
  vueSpec: {
    template: `
      <div class="alert alert--error">
        <svg class="alert__icon">...</svg>
        <div class="alert__content">
          <h4 class="alert__title">{{ title }}</h4>
          <p class="alert__message">{{ message }}</p>
        </div>
        <button class="alert__close" @click="$emit('close')">
          <svg>...</svg>
        </button>
      </div>
    `,
    props: {
      title: { type: String, default: 'Error' },
      message: { type: String, required: true }
    }
  }
}
```

### Modal/Dialog Extraction
```typescript
const modalComponent = {
  name: 'Confirmation Dialog',
  type: 'COMPONENT',
  layoutMode: 'VERTICAL',
  children: [
    {
      name: 'Header',
      type: 'FRAME',
      layoutMode: 'HORIZONTAL',
      children: [
        {
          name: 'Dialog Title',
          type: 'TEXT',
          characters: 'Confirm Action',
          style: { fontSize: 18, fontWeight: 600 }
        },
        {
          name: 'Close Button',
          type: 'INSTANCE',
          componentId: 'icon-button'
        }
      ]
    },
    {
      name: 'Content',
      type: 'FRAME',
      children: [
        {
          name: 'Body Text',
          type: 'TEXT',
          characters: 'Are you sure you want to proceed with this action?',
          style: { fontSize: 14, lineHeightPx: 20 }
        }
      ]
    },
    {
      name: 'Actions',
      type: 'FRAME',
      layoutMode: 'HORIZONTAL',
      itemSpacing: 12,
      children: [
        {
          name: 'Cancel Button',
          type: 'INSTANCE',
          componentId: 'button-secondary',
          textContent: 'Cancel'
        },
        {
          name: 'Confirm Button',
          type: 'INSTANCE',
          componentId: 'button-primary',
          textContent: 'Confirm'
        }
      ]
    }
  ],
  // Generated Vue component
  vueSpec: {
    template: `
      <div class="modal">
        <div class="modal__overlay" @click="$emit('close')"></div>
        <div class="modal__dialog">
          <div class="modal__header">
            <h3 class="modal__title">{{ title }}</h3>
            <button class="modal__close" @click="$emit('close')">×</button>
          </div>
          <div class="modal__content">
            <p>{{ message }}</p>
          </div>
          <div class="modal__actions">
            <button class="button button--secondary" @click="$emit('cancel')">
              {{ cancelText }}
            </button>
            <button class="button button--primary" @click="$emit('confirm')">
              {{ confirmText }}
            </button>
          </div>
        </div>
      </div>
    `,
    props: {
      title: { type: String, default: 'Confirm Action' },
      message: { type: String, required: true },
      cancelText: { type: String, default: 'Cancel' },
      confirmText: { type: String, default: 'Confirm' }
    }
  }
}
```

### Icon Button Extraction
```typescript
const iconButtonComponent = {
  name: 'Icon Button',
  type: 'COMPONENT_SET',
  variants: [
    {
      name: 'Default',
      type: 'COMPONENT',
      children: [
        {
          name: 'Icon',
          type: 'VECTOR',
          exportedSvg: '<svg viewBox="0 0 24 24"><path d="M12 4l8 8-8 8-1.4-1.4L16.2 13H4v-2h12.2l-5.6-5.6L12 4z"/></svg>',
          fills: [{ type: 'SOLID', color: { r: 0.4, g: 0.4, b: 0.4, a: 1 } }]
        }
      ],
      fills: [{ type: 'SOLID', color: { r: 1, g: 1, b: 1, a: 0 } }],
      cornerRadius: 4,
      padding: 8
    },
    {
      name: 'Hover',
      fills: [{ type: 'SOLID', color: { r: 0.95, g: 0.95, b: 0.95, a: 1 } }]
    },
    {
      name: 'Disabled',
      opacity: 0.4
    }
  ],
  vueSpec: {
    template: `
      <button 
        class="icon-button" 
        :class="{ 'icon-button--disabled': disabled }"
        :disabled="disabled"
        @click="$emit('click')"
      >
        <svg class="icon-button__icon" viewBox="0 0 24 24">
          <path d="M12 4l8 8-8 8-1.4-1.4L16.2 13H4v-2h12.2l-5.6-5.6L12 4z"/>
        </svg>
      </button>
    `,
    props: {
      disabled: { type: Boolean, default: false }
    }
  }
}
```

### Section with Header and Rows
```typescript
const sectionLayout = {
  name: 'Product List Section',
  type: 'FRAME',
  layoutMode: 'VERTICAL',
  itemSpacing: 16,
  children: [
    {
      name: 'Section Header',
      type: 'FRAME',
      layoutMode: 'HORIZONTAL',
      children: [
        {
          name: 'Heading',
          type: 'TEXT',
          characters: 'Featured Products',
          style: { fontSize: 24, fontWeight: 700 }
        },
        {
          name: 'View All Link',
          type: 'TEXT',
          characters: 'View All',
          style: { fontSize: 14, color: '#3b82f6' }
        }
      ]
    },
    {
      name: 'Product Rows',
      type: 'FRAME',
      layoutMode: 'VERTICAL',
      itemSpacing: 12,
      children: [
        {
          name: 'Product Row 1',
          type: 'INSTANCE',
          componentId: 'product-card'
        },
        {
          name: 'Product Row 2',
          type: 'INSTANCE',
          componentId: 'product-card'
        },
        {
          name: 'Product Row 3',
          type: 'INSTANCE',
          componentId: 'product-card'
        }
      ]
    }
  ],
  vueSpec: {
    template: `
      <section class="product-section">
        <header class="product-section__header">
          <h2 class="product-section__title">{{ title }}</h2>
          <a href="/products" class="product-section__link">View All</a>
        </header>
        <div class="product-section__list">
          <ProductCard 
            v-for="product in products" 
            :key="product.id"
            :product="product"
          />
        </div>
      </section>
    `,
    props: {
      title: { type: String, default: 'Featured Products' },
      products: { type: Array, required: true }
    }
  }
}
```
```typescript
const layoutInfo = {
  type: 'FRAME',
  layoutMode: 'VERTICAL',
  paddingTop: 24,
  paddingRight: 16,
  paddingBottom: 24,
  paddingLeft: 16,
  itemSpacing: 12,
  primaryAxisSizingMode: 'AUTO',
  counterAxisSizingMode: 'FIXED',
  // Converts to CSS:
  css: {
    display: 'flex',
    flexDirection: 'column',
    gap: '12px',
    padding: '24px 16px'
  }
}
```

### Color Extraction
```typescript
const colorExtraction = {
  fills: [
    {
      type: 'SOLID',
      color: { r: 0.239, g: 0.510, b: 0.965, a: 1 },
      // Converts to:
      hex: '#3b82f6',
      rgb: 'rgb(59, 130, 246)',
      cssVar: '--color-primary'
    }
  ],
  strokes: [
    {
      type: 'SOLID',
      color: { r: 0.898, g: 0.898, b: 0.898, a: 1 },
      strokeWeight: 1,
      strokeAlign: 'INSIDE',
      // Converts to:
      css: '1px solid #e5e5e5'
    }
  ]
}
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
## Form Component Extraction Examples

### Text Input Detection
```typescript
const textInputNode = {
  type: 'COMPONENT',
  name: 'Email Input',
  children: [
    {
      type: 'TEXT',
      name: 'Label',
      characters: 'Email Address',
      style: { fontSize: 14, fontWeight: 600 }
    },
    {
      type: 'FRAME',
      name: 'Input Field',
      layoutMode: 'HORIZONTAL',
      fills: [{ type: 'SOLID', color: { r: 1, g: 1, b: 1, a: 1 } }],
      strokes: [{ type: 'SOLID', color: { r: 0.8, g: 0.8, b: 0.8, a: 1 } }],
      cornerRadius: 4,
      children: [
        {
          type: 'TEXT',
          name: 'Placeholder',
          characters: 'Enter your email',
          style: { fontSize: 14, color: '#999' }
        }
      ]
    },
    {
      type: 'TEXT',
      name: 'Helper Text',
      characters: 'We'll never share your email',
      style: { fontSize: 12, color: '#666' }
    }
  ],
  // Detected as TEXT_INPUT
  componentType: 'text-input',
  formProps: {
    label: 'Email Address',
    placeholder: 'Enter your email',
    hint: 'We'll never share your email',
    type: 'email',
    required: true
  },
  // Maps to library component
  libraryMapping: {
    vuetify: 'v-text-field',
    quasar: 'q-input',
    primevue: 'InputText',
    elementPlus: 'el-input',
    antDesign: 'a-input'
  }
}
```

### Dropdown/Select Detection
```typescript
const dropdownNode = {
  type: 'COMPONENT',
  name: 'Country Select',
  children: [
    {
      type: 'TEXT',
      name: 'Label',
      characters: 'Country'
    },
    {
      type: 'FRAME',
      name: 'Select Field',
      children: [
        {
          type: 'TEXT',
          name: 'Selected Value',
          characters: 'Select a country'
        },
        {
          type: 'VECTOR',
          name: 'Dropdown Icon',
          // Chevron down icon
        }
      ]
    },
    {
      type: 'FRAME',
      name: 'Options List',
      children: [
        { type: 'TEXT', characters: 'United States' },
        { type: 'TEXT', characters: 'Canada' },
        { type: 'TEXT', characters: 'United Kingdom' },
        { type: 'TEXT', characters: 'Australia' }
      ]
    }
  ],
  // Detected as DROPDOWN
  componentType: 'dropdown',
  formProps: {
    label: 'Country',
    placeholder: 'Select a country',
    options: ['United States', 'Canada', 'United Kingdom', 'Australia'],
    multiple: false
  },
  // Maps to library component
  libraryMapping: {
    vuetify: 'v-select',
    quasar: 'q-select',
    primevue: 'Dropdown',
    elementPlus: 'el-select',
    antDesign: 'a-select'
  }
}
```

### Checkbox Detection
```typescript
const checkboxNode = {
  type: 'COMPONENT',
  name: 'Terms Checkbox',
  children: [
    {
      type: 'FRAME',
      name: 'Checkbox Box',
      fills: [{ type: 'SOLID', color: { r: 1, g: 1, b: 1, a: 1 } }],
      strokes: [{ type: 'SOLID', color: { r: 0.2, g: 0.4, b: 0.9, a: 1 } }],
      cornerRadius: 2,
      size: { width: 20, height: 20 },
      children: [
        {
          type: 'VECTOR',
          name: 'Check Icon',
          // Checkmark icon when checked
        }
      ]
    },
    {
      type: 'TEXT',
      name: 'Label',
      characters: 'I agree to the terms and conditions'
    }
  ],
  // Detected as CHECKBOX
  componentType: 'checkbox',
  formProps: {
    label: 'I agree to the terms and conditions',
    value: false,
    required: true
  },
  // Maps to library component
  libraryMapping: {
    vuetify: 'v-checkbox',
    quasar: 'q-checkbox',
    primevue: 'Checkbox',
    elementPlus: 'el-checkbox',
    antDesign: 'a-checkbox'
  }
}
```

### Button Detection with Variants
```typescript
const buttonNode = {
  type: 'COMPONENT_SET',
  name: 'Submit Button',
  variants: [
    {
      name: 'Default',
      fills: [{ type: 'SOLID', color: { r: 0.2, g: 0.4, b: 0.9, a: 1 } }],
      cornerRadius: 4,
      padding: { horizontal: 16, vertical: 8 },
      children: [
        {
          type: 'TEXT',
          characters: 'Submit',
          style: { fontSize: 14, fontWeight: 600, color: '#fff' }
        }
      ]
    },
    {
      name: 'Hover',
      fills: [{ type: 'SOLID', color: { r: 0.15, g: 0.35, b: 0.85, a: 1 } }]
    },
    {
      name: 'Disabled',
      fills: [{ type: 'SOLID', color: { r: 0.8, g: 0.8, b: 0.8, a: 1 } }],
      opacity: 0.6
    }
  ],
  // Detected as BUTTON
  componentType: 'button',
  formProps: {
    label: 'Submit',
    type: 'submit',
    variant: 'primary',
    disabled: false
  },
  // Maps to library component
  libraryMapping: {
    vuetify: 'v-btn',
    quasar: 'q-btn',
    primevue: 'Button',
    elementPlus: 'el-button',
    antDesign: 'a-button'
  }
}
```

### Filter/Search Component Detection
```typescript
const filterNode = {
  type: 'COMPONENT',
  name: 'Search Filter',
  children: [
    {
      type: 'FRAME',
      name: 'Search Input',
      children: [
        {
          type: 'VECTOR',
          name: 'Search Icon',
          // Magnifying glass icon
        },
        {
          type: 'TEXT',
          name: 'Placeholder',
          characters: 'Search products...'
        },
        {
          type: 'VECTOR',
          name: 'Clear Icon',
          // X icon to clear search
        }
      ]
    }
  ],
  // Detected as FILTER
  componentType: 'filter',
  formProps: {
    placeholder: 'Search products...',
    clearable: true,
    icon: 'search',
    type: 'search'
  },
  // Maps to library component
  libraryMapping: {
    vuetify: 'v-text-field (with prepend-inner-icon)',
    quasar: 'q-input (with prepend icon)',
    primevue: 'InputText (with icon)',
    elementPlus: 'el-input (with prefix-icon)',
    antDesign: 'a-input-search'
  }
}
```

### Complete Form Detection
```typescript
const formNode = {
  type: 'FRAME',
  name: 'User Registration Form',
  layoutMode: 'VERTICAL',
  itemSpacing: 16,
  children: [
    {
      type: 'TEXT',
      name: 'Form Title',
      characters: 'Create Account'
    },
    {
      type: 'INSTANCE',
      componentId: 'text-input',
      formType: 'text',
      label: 'Full Name',
      placeholder: 'Enter your name'
    },
    {
      type: 'INSTANCE',
      componentId: 'text-input',
      formType: 'email',
      label: 'Email',
      placeholder: 'Enter your email'
    },
    {
      type: 'INSTANCE',
      componentId: 'text-input',
      formType: 'password',
      label: 'Password',
      placeholder: 'Enter password'
    },
    {
      type: 'INSTANCE',
      componentId: 'dropdown',
      label: 'Country',
      options: ['USA', 'UK', 'Canada']
    },
    {
      type: 'INSTANCE',
      componentId: 'checkbox',
      label: 'I agree to terms'
    },
    {
      type: 'FRAME',
      name: 'Button Group',
      layoutMode: 'HORIZONTAL',
      itemSpacing: 12,
      children: [
        {
          type: 'INSTANCE',
          componentId: 'button',
          variant: 'secondary',
          label: 'Cancel'
        },
        {
          type: 'INSTANCE',
          componentId: 'button',
          variant: 'primary',
          label: 'Submit',
          type: 'submit'
        }
      ]
    }
  ],
  // Detected as FORM
  formAnalysis: {
    fields: [
      { name: 'fullName', type: 'text', required: true },
      { name: 'email', type: 'email', required: true },
      { name: 'password', type: 'password', required: true },
      { name: 'country', type: 'select', required: true },
      { name: 'agreedToTerms', type: 'checkbox', required: true }
    ],
    validation: true,
    submitButton: { label: 'Submit', type: 'submit' },
    cancelButton: { label: 'Cancel', type: 'button' }
  }
}
```
