# Component Generator Examples

## Basic Button Component

### Input Specification
```typescript
const buttonSpec: ComponentSpec = {
  name: 'BaseButton',
  type: 'presentation',
  description: 'A reusable button component with multiple variants',
  props: [
    { name: 'variant', type: 'String', required: false, default: 'primary' },
    { name: 'size', type: 'String', required: false, default: 'medium' },
    { name: 'disabled', type: 'Boolean', required: false, default: false },
    { name: 'loading', type: 'Boolean', required: false, default: false }
  ],
  events: [
    { name: 'click', payload: { type: 'MouseEvent' }, description: 'Emitted when button is clicked' }
  ],
  slots: [
    { name: 'default', description: 'Button content' },
    { name: 'icon', description: 'Optional icon slot' }
  ]
}
```

### Generated Vue Component
```vue
<template>
  <button
    :class="buttonClasses"
    :disabled="disabled || loading"
    :aria-busy="loading"
    @click="handleClick"
    data-testid="base-button"
  >
    <span v-if="loading" class="button__spinner" aria-hidden="true">
      <LoadingSpinner />
    </span>
    <slot name="icon" v-if="$slots.icon && !loading" />
    <span class="button__content">
      <slot>Button</slot>
    </span>
  </button>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue'
import LoadingSpinner from '@/components/LoadingSpinner.vue'

export default defineComponent({
  name: 'BaseButton',
  components: {
    LoadingSpinner
  },
  props: {
    variant: {
      type: String,
      default: 'primary',
      validator: (value: string) => ['primary', 'secondary', 'danger'].includes(value)
    },
    size: {
      type: String,
      default: 'medium',
      validator: (value: string) => ['small', 'medium', 'large'].includes(value)
    },
    disabled: {
      type: Boolean,
      default: false
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  emits: {
    click: (event: MouseEvent) => event instanceof MouseEvent
  },
  setup(props, { emit }) {
    const buttonClasses = computed(() => [
      'button',
      `button--${props.variant}`,
      `button--${props.size}`,
      {
        'button--disabled': props.disabled,
        'button--loading': props.loading
      }
    ])

    const handleClick = (event: MouseEvent) => {
      if (!props.disabled && !props.loading) {
        emit('click', event)
      }
    }

    return {
      buttonClasses,
      handleClick
    }
  }
})
</script>

<style lang="scss" scoped>
@import '@/styles/design-tokens';

.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: $spacing-2;
  border: none;
  border-radius: $border-radius-md;
  font-family: $font-family-sans;
  font-weight: $font-weight-medium;
  cursor: pointer;
  transition: all $transition-fast ease-in-out;

  &:focus-visible {
    outline: 2px solid $color-primary-500;
    outline-offset: 2px;
  }

  // Variants
  &--primary {
    background-color: $color-primary-500;
    color: $color-white;

    &:hover:not(:disabled) {
      background-color: $color-primary-600;
    }

    &:active:not(:disabled) {
      background-color: $color-primary-700;
    }
  }

  &--secondary {
    background-color: $color-gray-100;
    color: $color-gray-900;
    border: 1px solid $color-gray-300;

    &:hover:not(:disabled) {
      background-color: $color-gray-200;
    }
  }

  &--danger {
    background-color: $color-red-500;
    color: $color-white;

    &:hover:not(:disabled) {
      background-color: $color-red-600;
    }
  }

  // Sizes
  &--small {
    padding: $spacing-1 $spacing-3;
    font-size: $font-size-sm;
    height: 32px;
  }

  &--medium {
    padding: $spacing-2 $spacing-4;
    font-size: $font-size-base;
    height: 40px;
  }

  &--large {
    padding: $spacing-3 $spacing-6;
    font-size: $font-size-lg;
    height: 48px;
  }

  // States
  &--disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  &--loading {
    cursor: wait;
  }

  &__spinner {
    display: flex;
    align-items: center;
  }

  &__content {
    display: flex;
    align-items: center;
    gap: $spacing-2;
  }
}
</style>
```

## Product Card Component

### Input Specification
```typescript
const productCardSpec: ComponentSpec = {
  name: 'ProductCard',
  type: 'presentation',
  props: [
    { name: 'product', type: 'Object', required: true },
    { name: 'showActions', type: 'Boolean', default: true },
    { name: 'compact', type: 'Boolean', default: false }
  ],
  events: [
    { name: 'add-to-cart', payload: { type: 'Object', properties: { productId: 'string' } } },
    { name: 'view-details', payload: { type: 'Object', properties: { productId: 'string' } } }
  ]
}
```

### Generated Component
```vue
<template>
  <article 
    :class="cardClasses"
    data-testid="product-card"
  >
    <div class="product-card__image-container">
      <img 
        :src="product.image" 
        :alt="product.name"
        class="product-card__image"
        loading="lazy"
      />
      <div v-if="product.sale" class="product-card__badge">
        Sale
      </div>
    </div>
    
    <div class="product-card__content">
      <h3 class="product-card__title">{{ product.name }}</h3>
      <p v-if="!compact" class="product-card__description">
        {{ product.description }}
      </p>
      
      <div class="product-card__price">
        <span class="product-card__price-current">
          ${{ product.price }}
        </span>
        <span 
          v-if="product.originalPrice" 
          class="product-card__price-original"
        >
          ${{ product.originalPrice }}
        </span>
      </div>
      
      <div v-if="showActions" class="product-card__actions">
        <BaseButton
          variant="primary"
          @click="handleAddToCart"
          :aria-label="`Add ${product.name} to cart`"
        >
          Add to Cart
        </BaseButton>
        <BaseButton
          variant="secondary"
          @click="handleViewDetails"
        >
          View Details
        </BaseButton>
      </div>
    </div>
  </article>
</template>

<script lang="ts">
import { defineComponent, computed, PropType } from 'vue'
import BaseButton from '@/components/BaseButton.vue'

interface Product {
  id: string
  name: string
  description: string
  price: number
  originalPrice?: number
  image: string
  sale?: boolean
}

export default defineComponent({
  name: 'ProductCard',
  components: {
    BaseButton
  },
  props: {
    product: {
      type: Object as PropType<Product>,
      required: true
    },
    showActions: {
      type: Boolean,
      default: true
    },
    compact: {
      type: Boolean,
      default: false
    }
  },
  emits: {
    'add-to-cart': (payload: { productId: string }) => 
      typeof payload.productId === 'string',
    'view-details': (payload: { productId: string }) => 
      typeof payload.productId === 'string'
  },
  setup(props, { emit }) {
    const cardClasses = computed(() => [
      'product-card',
      {
        'product-card--compact': props.compact,
        'product-card--sale': props.product.sale
      }
    ])

    const handleAddToCart = () => {
      emit('add-to-cart', { productId: props.product.id })
    }

    const handleViewDetails = () => {
      emit('view-details', { productId: props.product.id })
    }

    return {
      cardClasses,
      handleAddToCart,
      handleViewDetails
    }
  }
})
</script>

<style lang="scss" scoped>
@import '@/styles/design-tokens';

.product-card {
  display: flex;
  flex-direction: column;
  background: $color-white;
  border-radius: $border-radius-lg;
  box-shadow: $shadow-sm;
  overflow: hidden;
  transition: transform $transition-fast ease-in-out,
              box-shadow $transition-fast ease-in-out;

  &:hover {
    transform: translateY(-2px);
    box-shadow: $shadow-md;
  }

  &--compact {
    flex-direction: row;

    .product-card__image-container {
      width: 120px;
      flex-shrink: 0;
    }

    .product-card__content {
      padding: $spacing-4;
    }
  }

  &__image-container {
    position: relative;
    aspect-ratio: 4 / 3;
    overflow: hidden;
  }

  &__image {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  &__badge {
    position: absolute;
    top: $spacing-2;
    right: $spacing-2;
    background: $color-red-500;
    color: $color-white;
    padding: $spacing-1 $spacing-2;
    border-radius: $border-radius-sm;
    font-size: $font-size-xs;
    font-weight: $font-weight-medium;
  }

  &__content {
    padding: $spacing-6;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
  }

  &__title {
    font-size: $font-size-lg;
    font-weight: $font-weight-semibold;
    color: $color-gray-900;
    margin-bottom: $spacing-2;
  }

  &__description {
    color: $color-gray-600;
    font-size: $font-size-sm;
    line-height: 1.5;
    margin-bottom: $spacing-4;
    flex-grow: 1;
  }

  &__price {
    display: flex;
    align-items: center;
    gap: $spacing-2;
    margin-bottom: $spacing-4;
  }

  &__price-current {
    font-size: $font-size-xl;
    font-weight: $font-weight-bold;
    color: $color-gray-900;
  }

  &__price-original {
    font-size: $font-size-base;
    color: $color-gray-500;
    text-decoration: line-through;
  }

  &__actions {
    display: flex;
    gap: $spacing-2;
    margin-top: auto;

    @media (max-width: $breakpoint-sm) {
      flex-direction: column;
    }
  }
}
</style>
```

## Form Input Component

### Input Specification
```typescript
const inputSpec: ComponentSpec = {
  name: 'FormInput',
  type: 'form',
  props: [
    { name: 'modelValue', type: 'String', required: true },
    { name: 'label', type: 'String', required: true },
    { name: 'type', type: 'String', default: 'text' },
    { name: 'placeholder', type: 'String', required: false },
    { name: 'error', type: 'String', required: false },
    { name: 'disabled', type: 'Boolean', default: false },
    { name: 'required', type: 'Boolean', default: false }
  ],
  events: [
    { name: 'update:modelValue', payload: { type: 'String' } },
    { name: 'blur', payload: { type: 'FocusEvent' } },
    { name: 'focus', payload: { type: 'FocusEvent' } }
  ]
}
```

This example shows how complex form components are generated with proper v-model support, validation states, and accessibility features.