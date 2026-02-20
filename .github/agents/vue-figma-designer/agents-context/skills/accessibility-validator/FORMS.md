# Accessibility Validator Forms

## Accessibility Audit Configuration

### Compliance Standards
```yaml
wcagLevel:
  type: select
  required: true
  options: ["A", "AA", "AAA"]
  default: "AA"
  description: "WCAG compliance level to validate against"

wcagVersion:
  type: select
  required: true
  options: ["2.1", "2.2"]
  default: "2.1"
  description: "WCAG version for validation"

standards:
  type: multiselect
  options: ["WCAG", "Section508", "ADA"]
  default: ["WCAG"]
  description: "Accessibility standards to check"
```

### Testing Scope
```yaml
testTypes:
  type: multiselect
  required: true
  options: 
    - "automated"
    - "keyboard-navigation" 
    - "screen-reader"
    - "color-contrast"
    - "semantic-html"
    - "aria-validation"
  default: ["automated", "keyboard-navigation", "color-contrast"]

components:
  type: multiselect
  description: "Specific components to validate (empty = all)"
  options: [] # Populated dynamically from project

includeViews:
  type: boolean
  default: true
  description: "Include page-level components in validation"
```

### Screen Reader Testing
```yaml
screenReaders:
  type: multiselect
  options: ["NVDA", "JAWS", "VoiceOver", "TalkBack"]
  default: ["NVDA", "VoiceOver"]
  description: "Screen readers to test compatibility"

manualTestInstructions:
  type: boolean
  default: true
  description: "Generate manual testing instructions"

announcementTesting:
  type: boolean
  default: true
  description: "Test dynamic content announcements"
```

### Keyboard Navigation
```yaml
keyboardPatterns:
  type: multiselect
  options:
    - "tab-navigation"
    - "arrow-navigation" 
    - "escape-handling"
    - "enter-activation"
    - "space-activation"
    - "focus-trapping"
  default: ["tab-navigation", "enter-activation", "escape-handling"]

customShortcuts:
  type: array
  items:
    key: 
      type: string
      description: "Key combination (e.g., 'Ctrl+K')"
    action:
      type: string
      description: "Expected action"
```

## Color and Visual Testing

### Color Contrast
```yaml
contrastRatios:
  normalText:
    type: number
    default: 4.5
    min: 3
    max: 21
    description: "Minimum contrast ratio for normal text"
  
  largeText:
    type: number 
    default: 3
    min: 3
    max: 21
    description: "Minimum contrast ratio for large text (18pt+)"

  uiComponents:
    type: number
    default: 3
    min: 3
    max: 21
    description: "Minimum contrast ratio for UI components"

backgroundColors:
  type: array
  description: "Additional background colors to test"
  items:
    color:
      type: color
      description: "Background color hex value"
```

### Visual Elements
```yaml
imageAlternatives:
  type: boolean
  default: true
  description: "Validate image alt text"

decorativeImages:
  type: boolean
  default: true
  description: "Check decorative images have empty alt"

videoCaptions:
  type: boolean
  default: true
  description: "Verify video caption support"

animationControls:
  type: boolean
  default: true
  description: "Check animation control options"
```

## Report Configuration

### Output Options
```yaml
reportFormats:
  type: multiselect
  options: ["html", "json", "csv", "junit"]
  default: ["html", "json"]
  description: "Accessibility report formats"

includeScreenshots:
  type: boolean
  default: false
  description: "Include screenshots in violation reports"

severityFilter:
  type: multiselect
  options: ["critical", "serious", "moderate", "minor"]
  default: ["critical", "serious", "moderate"]
  description: "Include violations of these severity levels"

groupByComponent:
  type: boolean
  default: true
  description: "Group violations by component"
```

### Remediation
```yaml
generateFixes:
  type: boolean
  default: true
  description: "Generate code fix suggestions"

includeExamples:
  type: boolean
  default: true
  description: "Include implementation examples"

prioritizeIssues:
  type: boolean
  default: true
  description: "Prioritize issues by impact and effort"

estimateEffort:
  type: boolean
  default: true
  description: "Estimate remediation effort"
```