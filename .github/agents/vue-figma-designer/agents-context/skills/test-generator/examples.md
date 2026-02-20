# Test Generator Examples

## Component Unit Tests

### Basic Button Component Test
```typescript
// BaseButton.spec.ts
import { mount } from '@vue/test-utils'
import { describe, it, expect, vi } from 'vitest'
import BaseButton from '@/components/BaseButton.vue'

describe('BaseButton', () => {
  const defaultProps = {
    variant: 'primary',
    size: 'medium'
  }

  describe('Mounting', () => {
    it('should mount successfully', () => {
      const wrapper = mount(BaseButton, {
        props: defaultProps,
        slots: {
          default: 'Click me'
        }
      })

      expect(wrapper.exists()).toBe(true)
      expect(wrapper.text()).toContain('Click me')
    })
  })

  describe('Props', () => {
    it.each([
      ['primary', 'button--primary'],
      ['secondary', 'button--secondary'],
      ['danger', 'button--danger']
    ])('should apply correct variant class for %s', (variant, expectedClass) => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, variant }
      })

      expect(wrapper.classes()).toContain(expectedClass)
    })

    it.each([
      ['small', 'button--small'],
      ['medium', 'button--medium'],
      ['large', 'button--large']
    ])('should apply correct size class for %s', (size, expectedClass) => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, size }
      })

      expect(wrapper.classes()).toContain(expectedClass)
    })

    it('should be disabled when disabled prop is true', () => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, disabled: true }
      })

      expect(wrapper.find('button').attributes()).toHaveProperty('disabled')
      expect(wrapper.classes()).toContain('button--disabled')
    })

    it('should show loading state', () => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, loading: true }
      })

      expect(wrapper.find('.button__spinner').exists()).toBe(true)
      expect(wrapper.attributes('aria-busy')).toBe('true')
    })
  })

  describe('Events', () => {
    it('should emit click event when clicked', async () => {
      const wrapper = mount(BaseButton, {
        props: defaultProps
      })

      await wrapper.trigger('click')

      expect(wrapper.emitted('click')).toBeTruthy()
      expect(wrapper.emitted('click')).toHaveLength(1)
    })

    it('should not emit click when disabled', async () => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, disabled: true }
      })

      await wrapper.trigger('click')

      expect(wrapper.emitted('click')).toBeFalsy()
    })

    it('should not emit click when loading', async () => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, loading: true }
      })

      await wrapper.trigger('click')

      expect(wrapper.emitted('click')).toBeFalsy()
    })
  })

  describe('Slots', () => {
    it('should render default slot content', () => {
      const wrapper = mount(BaseButton, {
        props: defaultProps,
        slots: {
          default: '<span>Custom content</span>'
        }
      })

      expect(wrapper.find('span').text()).toBe('Custom content')
    })

    it('should render icon slot when provided', () => {
      const wrapper = mount(BaseButton, {
        props: defaultProps,
        slots: {
          icon: '<i class="icon-star"></i>',
          default: 'Starred'
        }
      })

      expect(wrapper.find('.icon-star').exists()).toBe(true)
    })

    it('should not show icon slot when loading', () => {
      const wrapper = mount(BaseButton, {
        props: { ...defaultProps, loading: true },
        slots: {
          icon: '<i class="icon-star"></i>'
        }
      })

      expect(wrapper.find('.icon-star').exists()).toBe(false)
    })
  })
})
```

### Product Card Component Test
```typescript
// ProductCard.spec.ts
import { mount } from '@vue/test-utils'
import { describe, it, expect, vi } from 'vitest'
import ProductCard from '@/components/ProductCard.vue'
import BaseButton from '@/components/BaseButton.vue'

const mockProduct = {
  id: '1',
  name: 'Test Product',
  description: 'A great test product',
  price: 29.99,
  originalPrice: 39.99,
  image: '/test-image.jpg',
  sale: true
}

describe('ProductCard', () => {
  describe('Product Display', () => {
    it('should display product information correctly', () => {
      const wrapper = mount(ProductCard, {
        props: { product: mockProduct }
      })

      expect(wrapper.find('.product-card__title').text()).toBe(mockProduct.name)
      expect(wrapper.find('.product-card__description').text()).toBe(mockProduct.description)
      expect(wrapper.find('.product-card__price-current').text()).toBe(`$${mockProduct.price}`)
      expect(wrapper.find('.product-card__price-original').text()).toBe(`$${mockProduct.originalPrice}`)
    })

    it('should show sale badge when product is on sale', () => {
      const wrapper = mount(ProductCard, {
        props: { product: mockProduct }
      })

      expect(wrapper.find('.product-card__badge').exists()).toBe(true)
      expect(wrapper.find('.product-card__badge').text()).toBe('Sale')
    })

    it('should not show sale badge when product is not on sale', () => {
      const wrapper = mount(ProductCard, {
        props: { 
          product: { ...mockProduct, sale: false }
        }
      })

      expect(wrapper.find('.product-card__badge').exists()).toBe(false)
    })

    it('should apply compact layout when compact prop is true', () => {
      const wrapper = mount(ProductCard, {
        props: { 
          product: mockProduct,
          compact: true
        }
      })

      expect(wrapper.classes()).toContain('product-card--compact')
      expect(wrapper.find('.product-card__description').exists()).toBe(false)
    })
  })

  describe('Actions', () => {
    it('should emit add-to-cart event with product ID', async () => {
      const wrapper = mount(ProductCard, {
        props: { product: mockProduct },
        global: {
          components: { BaseButton }
        }
      })

      const addToCartButton = wrapper.findComponent({ name: 'BaseButton' })
      await addToCartButton.vm.$emit('click')

      expect(wrapper.emitted('add-to-cart')).toBeTruthy()
      expect(wrapper.emitted('add-to-cart')[0]).toEqual([{ productId: mockProduct.id }])
    })

    it('should emit view-details event with product ID', async () => {
      const wrapper = mount(ProductCard, {
        props: { product: mockProduct },
        global: {
          components: { BaseButton }
        }
      })

      const buttons = wrapper.findAllComponents({ name: 'BaseButton' })
      const viewDetailsButton = buttons[1] // Second button is view details
      await viewDetailsButton.vm.$emit('click')

      expect(wrapper.emitted('view-details')).toBeTruthy()
      expect(wrapper.emitted('view-details')[0]).toEqual([{ productId: mockProduct.id }])
    })

    it('should not show actions when showActions is false', () => {
      const wrapper = mount(ProductCard, {
        props: { 
          product: mockProduct,
          showActions: false
        }
      })

      expect(wrapper.find('.product-card__actions').exists()).toBe(false)
    })
  })

  describe('Image Loading', () => {
    it('should have lazy loading attribute', () => {
      const wrapper = mount(ProductCard, {
        props: { product: mockProduct }
      })

      const image = wrapper.find('.product-card__image')
      expect(image.attributes('loading')).toBe('lazy')
      expect(image.attributes('src')).toBe(mockProduct.image)
      expect(image.attributes('alt')).toBe(mockProduct.name)
    })
  })
})
```

## Store Testing Examples

### Pinia Store Test
```typescript
// productStore.spec.ts
import { setActivePinia, createPinia } from 'pinia'
import { describe, it, expect, vi, beforeEach } from 'vitest'
import { useProductStore } from '@/stores/productStore'
import * as productService from '@/services/productService'

// Mock the service
vi.mock('@/services/productService')

describe('Product Store', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
    vi.clearAllMocks()
  })

  describe('Initial State', () => {
    it('should have correct initial state', () => {
      const store = useProductStore()

      expect(store.products).toEqual([])
      expect(store.loading).toBe(false)
      expect(store.error).toBeNull()
      expect(store.selectedProduct).toBeNull()
    })
  })

  describe('Getters', () => {
    it('should return featured products', () => {
      const store = useProductStore()
      store.products = [
        { id: '1', name: 'Product 1', featured: true },
        { id: '2', name: 'Product 2', featured: false },
        { id: '3', name: 'Product 3', featured: true }
      ]

      expect(store.featuredProducts).toHaveLength(2)
      expect(store.featuredProducts.every(p => p.featured)).toBe(true)
    })

    it('should return product count', () => {
      const store = useProductStore()
      store.products = [
        { id: '1', name: 'Product 1' },
        { id: '2', name: 'Product 2' }
      ]

      expect(store.productCount).toBe(2)
    })

    it('should return hasProducts boolean', () => {
      const store = useProductStore()
      
      expect(store.hasProducts).toBe(false)
      
      store.products = [{ id: '1', name: 'Product 1' }]
      expect(store.hasProducts).toBe(true)
    })
  })

  describe('Actions', () => {
    describe('fetchProducts', () => {
      it('should fetch products successfully', async () => {
        const mockProducts = [
          { id: '1', name: 'Product 1', price: 10 },
          { id: '2', name: 'Product 2', price: 20 }
        ]

        vi.mocked(productService.getProducts).mockResolvedValue(mockProducts)

        const store = useProductStore()
        const result = await store.fetchProducts()

        expect(store.loading).toBe(false)
        expect(store.error).toBeNull()
        expect(store.products).toEqual(mockProducts)
        expect(result).toEqual(mockProducts)
        expect(productService.getProducts).toHaveBeenCalledOnce()
      })

      it('should handle fetch error', async () => {
        const errorMessage = 'Failed to fetch products'
        vi.mocked(productService.getProducts).mockRejectedValue(new Error(errorMessage))

        const store = useProductStore()

        await expect(store.fetchProducts()).rejects.toThrow(errorMessage)
        
        expect(store.loading).toBe(false)
        expect(store.error).toBe(errorMessage)
        expect(store.products).toEqual([])
      })

      it('should set loading state during fetch', async () => {
        let resolvePromise: (value: any) => void
        const promise = new Promise(resolve => {
          resolvePromise = resolve
        })

        vi.mocked(productService.getProducts).mockReturnValue(promise)

        const store = useProductStore()
        const fetchPromise = store.fetchProducts()

        expect(store.loading).toBe(true)

        resolvePromise!([])
        await fetchPromise

        expect(store.loading).toBe(false)
      })
    })

    describe('createProduct', () => {
      it('should create product successfully', async () => {
        const newProductData = { name: 'New Product', price: 15 }
        const createdProduct = { id: '3', ...newProductData }

        vi.mocked(productService.createProduct).mockResolvedValue(createdProduct)

        const store = useProductStore()
        const result = await store.createProduct(newProductData)

        expect(store.products).toContain(createdProduct)
        expect(result).toEqual(createdProduct)
        expect(productService.createProduct).toHaveBeenCalledWith(newProductData)
      })

      it('should handle creation error', async () => {
        const errorMessage = 'Failed to create product'
        vi.mocked(productService.createProduct).mockRejectedValue(new Error(errorMessage))

        const store = useProductStore()

        await expect(store.createProduct({})).rejects.toThrow(errorMessage)
        expect(store.error).toBe(errorMessage)
      })
    })
  })
})
```

## API Service Testing

### Service Test with MSW
```typescript
// productService.spec.ts
import { describe, it, expect, beforeAll, afterEach, afterAll } from 'vitest'
import { setupServer } from 'msw/node'
import { http, HttpResponse } from 'msw'
import { productService } from '@/services/productService'

const server = setupServer(
  http.get('/api/products', () => {
    return HttpResponse.json([
      { id: '1', name: 'Product 1', price: 10 },
      { id: '2', name: 'Product 2', price: 20 }
    ])
  }),

  http.post('/api/products', async ({ request }) => {
    const body = await request.json()
    return HttpResponse.json(
      { id: '3', ...body },
      { status: 201 }
    )
  }),

  http.get('/api/products/:id', ({ params }) => {
    const { id } = params
    return HttpResponse.json({
      id,
      name: `Product ${id}`,
      price: Number(id) * 10
    })
  })
)

describe('ProductService', () => {
  beforeAll(() => server.listen())
  afterEach(() => server.resetHandlers())
  afterAll(() => server.close())

  describe('getProducts', () => {
    it('should fetch all products', async () => {
      const products = await productService.getProducts()

      expect(products).toHaveLength(2)
      expect(products[0]).toEqual({ id: '1', name: 'Product 1', price: 10 })
    })

    it('should handle network error', async () => {
      server.use(
        http.get('/api/products', () => {
          return HttpResponse.error()
        })
      )

      await expect(productService.getProducts()).rejects.toThrow()
    })

    it('should handle 404 response', async () => {
      server.use(
        http.get('/api/products', () => {
          return new HttpResponse(null, { status: 404 })
        })
      )

      await expect(productService.getProducts()).rejects.toThrow()
    })
  })

  describe('getProduct', () => {
    it('should fetch single product by ID', async () => {
      const product = await productService.getProduct('1')

      expect(product).toEqual({
        id: '1',
        name: 'Product 1',
        price: 10
      })
    })

    it('should handle invalid ID', async () => {
      server.use(
        http.get('/api/products/invalid', () => {
          return new HttpResponse(null, { status: 404 })
        })
      )

      await expect(productService.getProduct('invalid')).rejects.toThrow()
    })
  })

  describe('createProduct', () => {
    it('should create new product', async () => {
      const newProductData = { name: 'New Product', price: 25 }
      const result = await productService.createProduct(newProductData)

      expect(result).toEqual({
        id: '3',
        name: 'New Product',
        price: 25
      })
    })

    it('should handle validation error', async () => {
      server.use(
        http.post('/api/products', () => {
          return HttpResponse.json(
            { error: 'Name is required' },
            { status: 400 }
          )
        })
      )

      await expect(
        productService.createProduct({ price: 10 })
      ).rejects.toThrow()
    })
  })
})
```

## Accessibility Testing Examples

### Component A11y Tests
```typescript
// ProductCard.a11y.spec.ts
import { mount } from '@vue/test-utils'
import { axe, toHaveNoViolations } from 'jest-axe'
import userEvent from '@testing-library/user-event'
import ProductCard from '@/components/ProductCard.vue'

expect.extend(toHaveNoViolations)

const mockProduct = {
  id: '1',
  name: 'Test Product',
  description: 'A great product',
  price: 29.99,
  image: '/test.jpg'
}

describe('ProductCard Accessibility', () => {
  it('should not have accessibility violations', async () => {
    const wrapper = mount(ProductCard, {
      props: { product: mockProduct }
    })

    const results = await axe(wrapper.element)
    expect(results).toHaveNoViolations()
  })

  it('should support keyboard navigation', async () => {
    const user = userEvent.setup()
    const wrapper = mount(ProductCard, {
      props: { product: mockProduct }
    })

    // Tab to first button
    await user.tab()
    expect(document.activeElement).toBe(
      wrapper.find('[data-testid="add-to-cart-btn"]').element
    )

    // Activate with Enter
    await user.keyboard('{Enter}')
    expect(wrapper.emitted('add-to-cart')).toBeTruthy()

    // Tab to second button
    await user.tab()
    expect(document.activeElement).toBe(
      wrapper.find('[data-testid="view-details-btn"]').element
    )

    // Activate with Space
    await user.keyboard(' ')
    expect(wrapper.emitted('view-details')).toBeTruthy()
  })

  it('should have proper ARIA labels', () => {
    const wrapper = mount(ProductCard, {
      props: { product: mockProduct }
    })

    const addButton = wrapper.find('[data-testid="add-to-cart-btn"]')
    expect(addButton.attributes('aria-label')).toBe(`Add ${mockProduct.name} to cart`)

    const image = wrapper.find('img')
    expect(image.attributes('alt')).toBe(mockProduct.name)
  })

  it('should announce price changes to screen readers', async () => {
    const wrapper = mount(ProductCard, {
      props: { product: mockProduct }
    })

    // Update price
    await wrapper.setProps({
      product: { ...mockProduct, price: 24.99 }
    })

    const liveRegion = wrapper.find('[aria-live="polite"]')
    expect(liveRegion.text()).toContain('Price updated to $24.99')
  })
})
```

## Visual Regression Testing

### Playwright Visual Tests
```typescript
// ProductCard.visual.spec.ts
import { test, expect } from '@playwright/test'

test.describe('ProductCard Visual Tests', () => {
  test('should match baseline appearance', async ({ page }) => {
    await page.goto('/storybook/iframe.html?id=components-productcard--default')
    
    await expect(page.locator('[data-testid="product-card"]')).toHaveScreenshot(
      'product-card-default.png'
    )
  })

  test('should match compact variant', async ({ page }) => {
    await page.goto('/storybook/iframe.html?id=components-productcard--compact')
    
    await expect(page.locator('[data-testid="product-card"]')).toHaveScreenshot(
      'product-card-compact.png'
    )
  })

  test('should match hover state', async ({ page }) => {
    await page.goto('/storybook/iframe.html?id=components-productcard--default')
    
    const card = page.locator('[data-testid="product-card"]')
    await card.hover()
    
    await expect(card).toHaveScreenshot('product-card-hover.png')
  })

  test('should be responsive across viewports', async ({ page }) => {
    await page.goto('/storybook/iframe.html?id=components-productcard--default')
    
    // Mobile
    await page.setViewportSize({ width: 375, height: 667 })
    await expect(page.locator('[data-testid="product-card"]')).toHaveScreenshot(
      'product-card-mobile.png'
    )
    
    // Tablet
    await page.setViewportSize({ width: 768, height: 1024 })
    await expect(page.locator('[data-testid="product-card"]')).toHaveScreenshot(
      'product-card-tablet.png'
    )
    
    // Desktop
    await page.setViewportSize({ width: 1280, height: 720 })
    await expect(page.locator('[data-testid="product-card"]')).toHaveScreenshot(
      'product-card-desktop.png'
    )
  })
})
```

This comprehensive set of examples demonstrates how the test generator creates thorough test coverage for Vue components, stores, services, accessibility, and visual regression testing.