# Accessibility Validator Examples

## Basic Component Validation

### Button Component Accessibility Test
```typescript
// Generated accessibility test for Button component
import { mount } from '@vue/test-utils'
import { axe, toHaveNoViolations } from 'jest-axe'
import { userEvent } from '@testing-library/user-event'
import BaseButton from '@/components/BaseButton.vue'

expect.extend(toHaveNoViolations)

describe('BaseButton Accessibility', () => {
  const defaultProps = {
    variant: 'primary',
    size: 'medium'
  }

  it('should pass axe accessibility audit', async () => {
    const wrapper = mount(BaseButton, {
      props: defaultProps,
      slots: { default: 'Click me' }
    })

    const results = await axe(wrapper.element)
    expect(results).toHaveNoViolations()
  })

  it('should support keyboard navigation', async () => {
    const user = userEvent.setup()
    const wrapper = mount(BaseButton, {
      props: defaultProps,
      slots: { default: 'Click me' }
    })

    // Tab to button
    await user.tab()
    expect(document.activeElement).toBe(wrapper.element)

    // Activate with Enter
    await user.keyboard('{Enter}')
    expect(wrapper.emitted('click')).toBeTruthy()

    // Activate with Space
    await user.keyboard(' ')
    expect(wrapper.emitted('click')).toHaveLength(2)
  })

  it('should have proper ARIA attributes', () => {
    const wrapper = mount(BaseButton, {
      props: { ...defaultProps, disabled: true },
      slots: { default: 'Disabled button' }
    })

    const button = wrapper.find('button')
    expect(button.attributes('aria-disabled')).toBe('true')
    expect(button.attributes('role')).toBe('button')
  })

  it('should announce loading state to screen readers', async () => {
    const wrapper = mount(BaseButton, {
      props: { ...defaultProps, loading: true },
      slots: { default: 'Loading...' }
    })

    const button = wrapper.find('button')
    expect(button.attributes('aria-busy')).toBe('true')
    
    // Check for live region
    const liveRegion = wrapper.find('[aria-live="polite"]')
    expect(liveRegion.exists()).toBe(true)
  })
})
```

### Form Input Accessibility Validation
```typescript
describe('FormInput Accessibility', () => {
  const defaultProps = {
    modelValue: '',
    label: 'Email Address',
    type: 'email',
    required: true
  }

  it('should have proper label association', () => {
    const wrapper = mount(FormInput, {
      props: defaultProps
    })

    const input = wrapper.find('input')
    const label = wrapper.find('label')
    
    expect(input.attributes('id')).toBeTruthy()
    expect(label.attributes('for')).toBe(input.attributes('id'))
    expect(input.attributes('aria-required')).toBe('true')
  })

  it('should announce validation errors', async () => {
    const wrapper = mount(FormInput, {
      props: {
        ...defaultProps,
        error: 'Email is required'
      }
    })

    const input = wrapper.find('input')
    const errorId = input.attributes('aria-describedby')
    const errorElement = wrapper.find(`#${errorId}`)
    
    expect(errorElement.exists()).toBe(true)
    expect(errorElement.text()).toBe('Email is required')
    expect(errorElement.attributes('role')).toBe('alert')
    expect(input.attributes('aria-invalid')).toBe('true')
  })

  it('should support screen reader instructions', () => {
    const wrapper = mount(FormInput, {
      props: {
        ...defaultProps,
        placeholder: 'Enter your email address',
        helpText: 'We will never share your email'
      }
    })

    const input = wrapper.find('input')
    const describedBy = input.attributes('aria-describedby')
    
    expect(describedBy).toContain('help')
    
    const helpElement = wrapper.find('[id*="help"]')
    expect(helpElement.text()).toBe('We will never share your email')
  })
})
```

## Complex Component Validation

### Modal Dialog Accessibility
```typescript
describe('ModalDialog Accessibility', () => {
  it('should trap focus within modal', async () => {
    const user = userEvent.setup()
    const wrapper = mount(ModalDialog, {
      props: {
        isOpen: true,
        title: 'Confirm Action'
      },
      slots: {
        default: `
          <p>Are you sure you want to proceed?</p>
          <button id="cancel">Cancel</button>
          <button id="confirm">Confirm</button>
        `
      }
    })

    // Focus should be on first focusable element
    await wrapper.vm.$nextTick()
    const firstFocusable = wrapper.find('#cancel')
    expect(document.activeElement).toBe(firstFocusable.element)

    // Tab should cycle through modal elements only
    await user.tab()
    expect(document.activeElement).toBe(wrapper.find('#confirm').element)
    
    await user.tab()
    expect(document.activeElement).toBe(wrapper.find('[aria-label="Close"]').element)
    
    await user.tab()
    expect(document.activeElement).toBe(firstFocusable.element)
  })

  it('should handle escape key correctly', async () => {
    const user = userEvent.setup()
    const wrapper = mount(ModalDialog, {
      props: {
        isOpen: true,
        title: 'Test Modal'
      }
    })

    await user.keyboard('{Escape}')
    expect(wrapper.emitted('close')).toBeTruthy()
  })

  it('should have proper ARIA labeling', () => {
    const wrapper = mount(ModalDialog, {
      props: {
        isOpen: true,
        title: 'Important Notice'
      }
    })

    const dialog = wrapper.find('[role="dialog"]')
    expect(dialog.exists()).toBe(true)
    expect(dialog.attributes('aria-modal')).toBe('true')
    expect(dialog.attributes('aria-labelledby')).toBeTruthy()
    
    const titleId = dialog.attributes('aria-labelledby')
    const titleElement = wrapper.find(`#${titleId}`)
    expect(titleElement.text()).toBe('Important Notice')
  })
})
```

### Data Table Accessibility
```typescript
describe('DataTable Accessibility', () => {
  const tableData = [
    { id: 1, name: 'John Doe', email: 'john@example.com', role: 'Admin' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com', role: 'User' }
  ]

  it('should have proper table structure', () => {
    const wrapper = mount(DataTable, {
      props: {
        data: tableData,
        columns: [
          { key: 'name', label: 'Name', sortable: true },
          { key: 'email', label: 'Email' },
          { key: 'role', label: 'Role' }
        ]
      }
    })

    // Check table semantics
    const table = wrapper.find('table')
    expect(table.exists()).toBe(true)
    
    const thead = wrapper.find('thead')
    const tbody = wrapper.find('tbody')
    expect(thead.exists()).toBe(true)
    expect(tbody.exists()).toBe(true)

    // Check header associations
    const headers = wrapper.findAll('th')
    headers.forEach(header => {
      expect(header.attributes('scope')).toBe('col')
    })
  })

  it('should support sortable column announcements', async () => {
    const user = userEvent.setup()
    const wrapper = mount(DataTable, {
      props: {
        data: tableData,
        columns: [
          { key: 'name', label: 'Name', sortable: true },
          { key: 'email', label: 'Email' }
        ]
      }
    })

    const sortableHeader = wrapper.find('th[aria-sort]')
    expect(sortableHeader.exists()).toBe(true)
    expect(sortableHeader.attributes('role')).toBe('columnheader button')
    
    await user.click(sortableHeader.element)
    expect(sortableHeader.attributes('aria-sort')).toBe('ascending')
    
    // Check for live region announcement
    const liveRegion = wrapper.find('[aria-live="polite"]')
    expect(liveRegion.text()).toContain('sorted by Name ascending')
  })

  it('should support keyboard navigation', async () => {
    const user = userEvent.setup()
    const wrapper = mount(DataTable, {
      props: {
        data: tableData,
        selectable: true
      }
    })

    // Test row selection with keyboard
    const firstRow = wrapper.find('tbody tr')
    firstRow.element.focus()
    
    await user.keyboard(' ')
    expect(firstRow.attributes('aria-selected')).toBe('true')
  })
})
```

## Accessibility Report Example

### Generated Accessibility Report
```typescript
const accessibilityReport: AccessibilityReport = {
  componentName: 'ProductCard',
  complianceLevel: 'AA',
  overallScore: 87,
  testDate: '2024-02-20T15:04:39.782Z',
  
  violations: [
    {
      id: 'color-contrast',
      impact: 'serious',
      tags: ['wcag2a', 'wcag143'],
      description: 'Elements must have sufficient color contrast',
      help: 'Ensure all text has sufficient contrast',
      helpUrl: 'https://dequeuniversity.com/rules/axe/4.6/color-contrast',
      nodes: [
        {
          target: ['.product-card__price-original'],
          html: '<span class="product-card__price-original">$19.99</span>',
          failureSummary: 'Element has insufficient color contrast of 2.8:1',
          element: null,
          impact: 'serious'
        }
      ]
    }
  ],
  
  warnings: [
    {
      id: 'image-alt',
      impact: 'minor',
      description: 'Image alt text could be more descriptive',
      nodes: [
        {
          target: ['.product-card__image'],
          html: '<img src="product.jpg" alt="Product">',
          suggestion: 'Use more descriptive alt text like "Blue cotton t-shirt"'
        }
      ]
    }
  ],
  
  passes: [
    {
      id: 'button-name',
      description: 'Buttons have accessible names',
      nodes: 2
    },
    {
      id: 'landmark-one-main',
      description: 'Document has proper landmark structure',
      nodes: 1
    }
  ],
  
  remediation: {
    immediate: [
      {
        issue: 'color-contrast',
        priority: 'high',
        effort: 'low',
        fix: 'Change color from #999 to #666 for sufficient contrast',
        codeExample: `
.product-card__price-original {
  color: #666; // Changed from #999 for better contrast
  text-decoration: line-through;
}`
      }
    ],
    
    recommended: [
      {
        issue: 'image-alt',
        priority: 'medium',
        effort: 'low',
        fix: 'Improve alt text to be more descriptive',
        codeExample: `
<img 
  :src="product.image" 
  :alt="\`\${product.name} - \${product.color} \${product.category}\`"
  class="product-card__image"
/>`
      }
    ]
  },
  
  keyboardTesting: {
    passed: true,
    focusOrder: [
      { element: 'button.add-to-cart', tabIndex: 0 },
      { element: 'button.view-details', tabIndex: 0 }
    ],
    issues: []
  },
  
  screenReaderTesting: {
    nvda: {
      compatible: true,
      issues: [],
      announcements: [
        'Product card region',
        'Blue Cotton T-Shirt heading level 3',
        'Price 14 dollars and 99 cents',
        'Add to Cart button'
      ]
    }
  }
}
```

### Manual Testing Instructions
```markdown
# Manual Accessibility Testing - ProductCard Component

## Screen Reader Testing

### NVDA (Windows)
1. Navigate to product card with Tab key
2. Expected announcement: "Product card region"
3. Press Down Arrow to read content
4. Verify price is announced correctly
5. Tab to buttons and verify they announce properly

### VoiceOver (macOS)
1. Use VO+Right Arrow to navigate through card
2. Verify heading is announced with level
3. Check that price information is clear
4. Test button activation with VO+Space

## Keyboard Navigation
1. Tab through all interactive elements
2. Verify focus indicators are visible
3. Test Enter and Space key activation
4. Check Escape key behavior in modal states

## Visual Testing
1. Increase zoom to 200% - layout should remain usable
2. Test with high contrast mode enabled
3. Verify focus indicators meet minimum size requirements
4. Check color-only information has additional indicators
```

This comprehensive example shows how the accessibility validator generates detailed reports with specific violations, remediation steps, and testing instructions for manual verification.