# Accessibility Validator Skill

## Overview
Validates and ensures WCAG 2.1 AA compliance across generated Vue components, implementing automated accessibility checks, semantic HTML validation, and assistive technology compatibility testing.

## Capabilities
- WCAG 2.1 AA compliance validation
- Semantic HTML structure analysis
- ARIA attribute validation and optimization
- Keyboard navigation testing
- Screen reader compatibility verification
- Color contrast ratio checking
- Focus management validation
- Automated accessibility testing generation

## Usage
This skill automatically validates accessibility during component generation and provides detailed reports with remediation recommendations.

## Key Functions
- `validateWCAGCompliance()` - Comprehensive WCAG validation
- `checkSemanticHTML()` - HTML structure and semantics validation
- `validateARIA()` - ARIA attributes and roles verification
- `testKeyboardNavigation()` - Keyboard accessibility testing
- `verifyScreenReader()` - Screen reader compatibility checks
- `checkColorContrast()` - Color contrast ratio validation

## Validation Areas
1. **Perceivable** - Text alternatives, color contrast, adaptable content
2. **Operable** - Keyboard accessible, timing adjustable, seizure prevention
3. **Understandable** - Readable, predictable, input assistance
4. **Robust** - Compatible with assistive technologies

## Output
- Detailed accessibility audit reports
- Automated test suites for accessibility
- Remediation recommendations with code examples
- Compliance scorecards and metrics
- Screen reader testing instructions

## Integration
- Axe-core for automated testing
- NVDA, JAWS, VoiceOver compatibility
- Keyboard navigation patterns
- Focus management strategies