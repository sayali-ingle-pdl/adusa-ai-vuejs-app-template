# Component Generator Skill

## Overview
Generates production-ready Vue 3 Single File Components (SFCs) from Figma design specifications using the Options API pattern with TypeScript support. **Integrates with existing component libraries and custom components** to maximize code reuse.

## Capabilities
- **Analyze project context** and available component libraries
- **Reuse existing library components** (Vuetify, Quasar, PrimeVue, Element Plus, Ant Design Vue)
- **Leverage existing custom components** when available
- Generate Vue 3 SFCs with proper structure and organization
- TypeScript integration with strict type checking
- Props definition with validation and defaults
- Event handling and emission patterns
- Computed properties and watchers
- Lifecycle hook implementation
- SCSS styling with design token integration
- Accessibility attributes and ARIA support

## Usage
This skill processes component specifications from the Figma parser **AND project analysis** to generate components that integrate seamlessly with existing code and component libraries.

## Execution Flow

### Phase 1: Project Context Analysis
1. **Load project analysis** from project-analyzer skill
2. **Identify component library** (if configured via `useComponentLibrary`)
3. **Build component catalog** of available library and custom components
4. **Extract coding conventions** (naming, structure, patterns)
5. **Load design system tokens** from existing theme files

### Phase 2: Component Matching
1. **Analyze Figma element** from parsed hierarchy
2. **Determine element type** (button, input, dialog, card, etc.)
3. **Search for existing matches**:
   - Check custom components first
   - Check library components if configured
   - Consider component composition (combining existing components)
4. **Select generation strategy**:
   - **Reuse**: Use existing component with props mapping
   - **Extend**: Extend existing component with modifications
   - **Compose**: Combine multiple existing components
   - **Generate**: Create new custom component following conventions

### Phase 3: Component Generation
Based on selected strategy, generate appropriate code

## Key Functions
- `generateVueComponent()` - Main component generation entry point
- `analyzeProjectContext()` - **Loads project analysis and component library info**
- `matchExistingComponent()` - **Finds matching library or custom components**
- `mapFigmaToLibraryProps()` - **Maps Figma properties to library component props**
- `generateWithLibrary()` - **Generates component using library components**
- `generateWithCustom()` - **Generates component reusing custom components**
- `generateComponentScript()` - Creates the script section with Options API
- `generateComponentTemplate()` - Builds the template with proper structure
- `generateComponentStyles()` - Generates SCSS with design tokens
- `validateComponentStructure()` - Ensures component follows best practices

## Component Generation Strategies

### Strategy 1: Use Library Component
When a matching library component exists:

```vue
<!-- Generated using Vuetify -->
<template>
  <v-btn
    :color="variant === 'primary' ? 'primary' : 'secondary'"
    :size="size"
    :disabled="disabled"
    @click="handleClick"
  >
    <v-icon v-if="icon" :icon="icon" start />
    {{ label }}
  </v-btn>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'

export default defineComponent({
  name: 'CustomButton',
  props: {
    variant: {
      type: String as PropType<'primary' | 'secondary'>,
      default: 'primary'
    },
    size: {
      type: String as PropType<'small' | 'default' | 'large'>,
      default: 'default'
    },
    icon: String,
    label: {
      type: String,
      required: true
    },
    disabled: Boolean
  },
  emits: ['click'],
  methods: {
    handleClick(event: MouseEvent) {
      if (!this.disabled) {
        this.$emit('click', event)
      }
    }
  }
})
</script>
```

### Strategy 2: Compose with Multiple Components
Combining library components for complex UI:

```vue
<!-- Generated Modal using Vuetify components -->
<template>
  <v-dialog v-model="isOpen" max-width="500">
    <v-card>
      <v-card-title>
        {{ title }}
        <v-spacer />
        <v-btn icon="mdi-close" variant="text" @click="close" />
      </v-card-title>
      
      <v-card-text>
        <slot>{{ message }}</slot>
      </v-card-text>
      
      <v-card-actions>
        <v-spacer />
        <v-btn color="secondary" @click="cancel">
          {{ cancelText }}
        </v-btn>
        <v-btn color="primary" @click="confirm">
          {{ confirmText }}
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'ConfirmDialog',
  props: {
    modelValue: Boolean,
    title: {
      type: String,
      default: 'Confirm Action'
    },
    message: String,
    cancelText: {
      type: String,
      default: 'Cancel'
    },
    confirmText: {
      type: String,
      default: 'Confirm'
    }
  },
  emits: ['update:modelValue', 'confirm', 'cancel'],
  computed: {
    isOpen: {
      get(): boolean {
        return this.modelValue
      },
      set(value: boolean) {
        this.$emit('update:modelValue', value)
      }
    }
  },
  methods: {
    close() {
      this.isOpen = false
    },
    confirm() {
      this.$emit('confirm')
      this.close()
    },
    cancel() {
      this.$emit('cancel')
      this.close()
    }
  }
})
</script>
```

### Strategy 3: Extend Existing Custom Component
When similar custom component exists:

```vue
<template>
  <BaseCard :variant="variant" :elevated="elevated">
    <template #header>
      <h3 class="product-card__title">{{ product.name }}</h3>
      <span class="product-card__price">{{ formattedPrice }}</span>
    </template>
    
    <template #default>
      <img 
        :src="product.imageUrl" 
        :alt="product.name"
        class="product-card__image"
      />
      <p class="product-card__description">{{ product.description }}</p>
    </template>
    
    <template #actions>
      <BaseButton variant="primary" @click="addToCart">
        Add to Cart
      </BaseButton>
    </template>
  </BaseCard>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'
import BaseCard from '@/components/common/BaseCard.vue'
import BaseButton from '@/components/common/BaseButton.vue'
import type { Product } from '@/interfaces/Product'

export default defineComponent({
  name: 'ProductCard',
  components: { BaseCard, BaseButton },
  props: {
    product: {
      type: Object as PropType<Product>,
      required: true
    },
    variant: {
      type: String as PropType<'flat' | 'elevated' | 'outlined'>,
      default: 'elevated'
    },
    elevated: Boolean
  },
  emits: ['add-to-cart'],
  computed: {
    formattedPrice(): string {
      return new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD'
      }).format(this.product.price)
    }
  },
  methods: {
    addToCart() {
      this.$emit('add-to-cart', this.product)
    }
  }
})
</script>

<style lang="scss" scoped>
@import '@/theme/vars.scss';

.product-card {
  &__title {
    font-size: $font-size-lg;
    font-weight: $font-weight-semibold;
    color: $color-text-primary;
  }
  
  &__price {
    font-size: $font-size-xl;
    font-weight: $font-weight-bold;
    color: $color-primary;
  }
  
  &__image {
    width: 100%;
    height: 200px;
    object-fit: cover;
    border-radius: $border-radius-md;
  }
  
  &__description {
    margin-top: $spacing-3;
    color: $color-text-secondary;
  }
}
</style>
```

### Strategy 4: Generate New with Project Conventions
When no match exists, follow project patterns:

```vue
<template>
  <div 
    class="custom-component"
    :class="componentClasses"
    role="region"
    :aria-label="ariaLabel"
  >
    <slot />
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'

export default defineComponent({
  name: 'CustomComponent',
  props: {
    variant: {
      type: String as PropType<'default' | 'primary' | 'secondary'>,
      default: 'default'
    },
    ariaLabel: String
  },
  computed: {
    componentClasses(): Record<string, boolean> {
      return {
        [`custom-component--${this.variant}`]: true
      }
    }
  }
})
</script>

<style lang="scss" scoped>
@import '@/theme/vars.scss';

.custom-component {
  // Generated following project conventions
}
</style>
```

## Component Library Integration

### Supported Libraries

**Vuetify**
- Components: `v-btn`, `v-card`, `v-dialog`, `v-text-field`, `v-select`, etc.
- Theming: Material Design 3
- Props mapping: Figma → Vuetify props

**Quasar**
- Components: `q-btn`, `q-card`, `q-dialog`, `q-input`, `q-select`, etc.
- Theming: Material Design + iOS
- Props mapping: Figma → Quasar props

**PrimeVue**
- Components: `Button`, `Card`, `Dialog`, `InputText`, `Dropdown`, etc.
- Theming: Multiple themes available
- Props mapping: Figma → PrimeVue props

**Element Plus**
- Components: `el-button`, `el-card`, `el-dialog`, `el-input`, `el-select`, etc.
- Theming: Element Design
- Props mapping: Figma → Element Plus props

**Ant Design Vue**
- Components: `a-button`, `a-card`, `a-modal`, `a-input`, `a-select`, etc.
- Theming: Ant Design
- Props mapping: Figma → Ant Design Vue props

## Component Structure
Generated components follow the standard Vue SFC structure:
1. `<template>` - Semantic HTML with accessibility
2. `<script lang="ts">` - Options API with TypeScript
3. `<style lang="scss" scoped>` - Styled with design tokens

## Features
- **Component library integration** - Reuses library components
- **Existing component reuse** - Leverages custom components
- **Convention following** - Matches project patterns
- Proper prop validation and typing
- Event emission with payload types
- Slot management with fallback content
- Responsive design integration
- Performance optimizations
- Testing hooks and data attributes

## Quality Assurance
- Vue 3 best practices compliance
- Component library best practices
- TypeScript strict mode compatibility
- Accessibility standards adherence
- Performance optimization patterns
- Code maintainability standards
- Project convention adherence