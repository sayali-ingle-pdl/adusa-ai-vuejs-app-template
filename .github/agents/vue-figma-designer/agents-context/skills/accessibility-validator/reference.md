# Accessibility Validator API Reference

## Core Validation Functions

### WCAG Compliance Validation
```typescript
function validateWCAGCompliance(
  component: GeneratedComponent,
  level: 'A' | 'AA' | 'AAA' = 'AA'
): AccessibilityReport

interface AccessibilityReport {
  componentName: string
  complianceLevel: 'A' | 'AA' | 'AAA'
  overallScore: number
  violations: Violation[]
  warnings: Warning[]
  passes: Pass[]
  remediation: RemediationPlan
}
```

### Automated Testing with Axe
```typescript
function runAxeAudit(
  element: HTMLElement,
  options: AxeRunOptions
): Promise<AxeResults>

interface AxeRunOptions {
  rules?: Record<string, { enabled: boolean }>
  tags?: string[]
  locale?: string
  reporter?: 'v1' | 'v2'
  runOnly?: {
    type: 'tag' | 'rule'
    values: string[]
  }
}
```

### Keyboard Navigation Testing
```typescript
function testKeyboardNavigation(
  component: GeneratedComponent,
  patterns: KeyboardPattern[]
): KeyboardTestResult

interface KeyboardPattern {
  type: 'tab' | 'arrow' | 'enter' | 'space' | 'escape'
  sequence: string[]
  expectedResult: ExpectedResult
}

interface KeyboardTestResult {
  passed: boolean
  failures: KeyboardFailure[]
  focusOrder: FocusableElement[]
  trapTesting: FocusTrapResult
}
```

### Screen Reader Compatibility
```typescript
function validateScreenReaderSupport(
  component: GeneratedComponent,
  screenReaders: ScreenReader[]
): ScreenReaderReport

interface ScreenReaderReport {
  compatible: boolean
  issues: ScreenReaderIssue[]
  recommendations: ScreenReaderRecommendation[]
  testInstructions: ManualTestInstruction[]
}

type ScreenReader = 'NVDA' | 'JAWS' | 'VoiceOver' | 'TalkBack'
```

## Validation Rules

### WCAG 2.1 Success Criteria
```typescript
const WCAG_SUCCESS_CRITERIA = {
  // Perceivable
  '1.1.1': 'Non-text Content',
  '1.2.1': 'Audio-only and Video-only (Prerecorded)',
  '1.2.2': 'Captions (Prerecorded)',
  '1.2.3': 'Audio Description or Media Alternative',
  '1.3.1': 'Info and Relationships',
  '1.3.2': 'Meaningful Sequence',
  '1.3.3': 'Sensory Characteristics',
  '1.4.1': 'Use of Color',
  '1.4.2': 'Audio Control',
  '1.4.3': 'Contrast (Minimum)',
  '1.4.4': 'Resize text',
  '1.4.5': 'Images of Text',
  
  // Operable
  '2.1.1': 'Keyboard',
  '2.1.2': 'No Keyboard Trap',
  '2.1.4': 'Character Key Shortcuts',
  '2.2.1': 'Timing Adjustable',
  '2.2.2': 'Pause, Stop, Hide',
  '2.3.1': 'Three Flashes or Below Threshold',
  '2.4.1': 'Bypass Blocks',
  '2.4.2': 'Page Titled',
  '2.4.3': 'Focus Order',
  '2.4.4': 'Link Purpose (In Context)',
  '2.4.5': 'Multiple Ways',
  '2.4.6': 'Headings and Labels',
  '2.4.7': 'Focus Visible',
  
  // Understandable
  '3.1.1': 'Language of Page',
  '3.2.1': 'On Focus',
  '3.2.2': 'On Input',
  '3.3.1': 'Error Identification',
  '3.3.2': 'Labels or Instructions',
  
  // Robust
  '4.1.1': 'Parsing',
  '4.1.2': 'Name, Role, Value',
  '4.1.3': 'Status Messages'
}
```

### Color Contrast Requirements
```typescript
interface ContrastRequirement {
  normalText: {
    AA: 4.5,
    AAA: 7.0
  }
  largeText: {
    AA: 3.0,
    AAA: 4.5
  }
  uiComponents: {
    AA: 3.0,
    AAA: 4.5
  }
}

function calculateContrastRatio(
  foreground: string,
  background: string
): number

function meetsContrastRequirement(
  ratio: number,
  level: 'AA' | 'AAA',
  textSize: 'normal' | 'large' | 'ui'
): boolean
```

### ARIA Validation Rules
```typescript
const ARIA_ROLES = [
  'alert', 'alertdialog', 'application', 'article', 'banner',
  'button', 'checkbox', 'columnheader', 'combobox', 'complementary',
  'contentinfo', 'dialog', 'directory', 'document', 'form',
  'grid', 'gridcell', 'group', 'heading', 'img', 'link', 'list',
  'listbox', 'listitem', 'log', 'main', 'marquee', 'math',
  'menu', 'menubar', 'menuitem', 'navigation', 'note',
  'option', 'presentation', 'progressbar', 'radio', 'radiogroup',
  'region', 'row', 'rowgroup', 'rowheader', 'scrollbar',
  'search', 'separator', 'slider', 'spinbutton', 'status',
  'tab', 'tablist', 'tabpanel', 'textbox', 'timer', 'toolbar',
  'tooltip', 'tree', 'treegrid', 'treeitem'
]

const ARIA_PROPERTIES = [
  'aria-activedescendant', 'aria-atomic', 'aria-autocomplete',
  'aria-busy', 'aria-checked', 'aria-controls', 'aria-describedby',
  'aria-disabled', 'aria-dropeffect', 'aria-expanded', 'aria-flowto',
  'aria-grabbed', 'aria-haspopup', 'aria-hidden', 'aria-invalid',
  'aria-label', 'aria-labelledby', 'aria-level', 'aria-live',
  'aria-multiline', 'aria-multiselectable', 'aria-orientation',
  'aria-owns', 'aria-posinset', 'aria-pressed', 'aria-readonly',
  'aria-relevant', 'aria-required', 'aria-selected', 'aria-setsize',
  'aria-sort', 'aria-valuemax', 'aria-valuemin', 'aria-valuenow',
  'aria-valuetext'
]
```

## Testing Patterns

### Focus Management
```typescript
function validateFocusManagement(element: HTMLElement): FocusValidationResult {
  return {
    hasFocusableElements: boolean
    focusOrder: FocusableElement[]
    trapValidation: FocusTrapValidation
    initialFocus: FocusResult
    returnFocus: FocusResult
  }
}

interface FocusableElement {
  element: HTMLElement
  tabIndex: number
  visible: boolean
  role?: string
  ariaLabel?: string
}
```

### Live Region Testing
```typescript
function validateLiveRegions(component: GeneratedComponent): LiveRegionResult {
  return {
    regions: LiveRegion[]
    announcements: AnnouncementTest[]
    politeness: PolitenessLevel[]
  }
}

interface LiveRegion {
  element: HTMLElement
  ariaLive: 'off' | 'polite' | 'assertive'
  ariaAtomic: boolean
  ariaRelevant: string
}
```

## Error Types and Severity

### Violation Severity Levels
```typescript
type ViolationSeverity = 'critical' | 'serious' | 'moderate' | 'minor'

interface Violation {
  id: string
  impact: ViolationSeverity
  tags: string[]
  description: string
  help: string
  helpUrl: string
  nodes: ViolationNode[]
}

interface ViolationNode {
  target: string[]
  html: string
  failureSummary: string
  element: HTMLElement
}
```

### Common Violations
- Missing alternative text for images
- Insufficient color contrast
- Missing form labels
- Invalid ARIA usage
- Keyboard navigation issues
- Missing skip links
- Improper heading hierarchy
- Duplicate IDs
- Missing landmark regions