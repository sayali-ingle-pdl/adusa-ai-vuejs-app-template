# Theme Skill

## Purpose
Generate SCSS theme files with variables and mixins.

## Output
Create the files:
- `src/theme/_variables.scss`
- `src/theme/_mixins.scss`
- `src/theme/index.scss`

## Template: src/theme/_variables.scss

```scss
// Color palette
$color-primary: #0066cc;
$color-secondary: #6c757d;
$color-success: #28a745;
$color-warning: #ffc107;
$color-error: #dc3545;
$color-info: #17a2b8;

// Text colors
$color-text-primary: #212529;
$color-text-secondary: #6c757d;
$color-text-disabled: #adb5bd;
$color-text-white: #ffffff;

// Background colors
$color-bg-primary: #ffffff;
$color-bg-secondary: #f8f9fa;
$color-bg-tertiary: #e9ecef;

// Border colors
$color-border-light: #dee2e6;
$color-border-medium: #ced4da;
$color-border-dark: #adb5bd;

// Spacing scale
$spacing-xs: 0.25rem;   // 4px
$spacing-sm: 0.5rem;    // 8px
$spacing-md: 1rem;      // 16px
$spacing-lg: 1.5rem;    // 24px
$spacing-xl: 2rem;      // 32px
$spacing-xxl: 3rem;     // 48px

// Typography
$font-family-base: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
  'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
$font-family-monospace: 'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, Courier, monospace;

$font-size-xs: 0.75rem;   // 12px
$font-size-sm: 0.875rem;  // 14px
$font-size-base: 1rem;    // 16px
$font-size-lg: 1.125rem;  // 18px
$font-size-xl: 1.25rem;   // 20px
$font-size-xxl: 1.5rem;   // 24px

$font-weight-light: 300;
$font-weight-normal: 400;
$font-weight-medium: 500;
$font-weight-semibold: 600;
$font-weight-bold: 700;

$line-height-base: 1.5;
$line-height-tight: 1.25;
$line-height-loose: 1.75;

// Borders
$border-radius-sm: 0.25rem;
$border-radius-md: 0.375rem;
$border-radius-lg: 0.5rem;
$border-radius-full: 9999px;

$border-width-thin: 1px;
$border-width-medium: 2px;
$border-width-thick: 4px;

// Shadows
$shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
$shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
$shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
$shadow-xl: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);

// Transitions
$transition-fast: 150ms ease-in-out;
$transition-base: 300ms ease-in-out;
$transition-slow: 500ms ease-in-out;

// Z-index scale
$z-index-dropdown: 1000;
$z-index-sticky: 1020;
$z-index-fixed: 1030;
$z-index-modal-backdrop: 1040;
$z-index-modal: 1050;
$z-index-popover: 1060;
$z-index-tooltip: 1070;

// Breakpoints
$breakpoint-xs: 0;
$breakpoint-sm: 576px;
$breakpoint-md: 768px;
$breakpoint-lg: 992px;
$breakpoint-xl: 1200px;
$breakpoint-xxl: 1400px;
```

## Template: src/theme/_mixins.scss

```scss
// Responsive breakpoint mixins
@mixin respond-to($breakpoint) {
  @if $breakpoint == 'xs' {
    @media (min-width: $breakpoint-xs) {
      @content;
    }
  } @else if $breakpoint == 'sm' {
    @media (min-width: $breakpoint-sm) {
      @content;
    }
  } @else if $breakpoint == 'md' {
    @media (min-width: $breakpoint-md) {
      @content;
    }
  } @else if $breakpoint == 'lg' {
    @media (min-width: $breakpoint-lg) {
      @content;
    }
  } @else if $breakpoint == 'xl' {
    @media (min-width: $breakpoint-xl) {
      @content;
    }
  } @else if $breakpoint == 'xxl' {
    @media (min-width: $breakpoint-xxl) {
      @content;
    }
  }
}

// Flexbox utilities
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin flex-between {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

// Truncate text
@mixin truncate {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

// Multi-line truncate
@mixin line-clamp($lines: 2) {
  display: -webkit-box;
  -webkit-line-clamp: $lines;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

// Visually hidden (accessible)
@mixin visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

// Focus outline
@mixin focus-outline {
  outline: 2px solid $color-primary;
  outline-offset: 2px;
}

// Card style
@mixin card {
  background: $color-bg-primary;
  border: $border-width-thin solid $color-border-light;
  border-radius: $border-radius-md;
  box-shadow: $shadow-sm;
}

// Button reset
@mixin button-reset {
  background: none;
  border: none;
  padding: 0;
  font: inherit;
  cursor: pointer;
  outline: inherit;
}
```

## Template: src/theme/index.scss

```scss
@forward 'variables';
@forward 'mixins';
```

## Notes
- Variables provide a comprehensive design system
- Mixins offer reusable style patterns
- Forward/use pattern enables clean imports in components
- Use with `@use '@/theme/' as *;` in components
