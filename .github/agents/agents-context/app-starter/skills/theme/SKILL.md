# Theme Skill

## Purpose
Generate SCSS theme files with variables and shadows.

## Output
Create the files:
- `src/theme/vars.scss`
- `src/theme/shadow.scss`
- `src/theme/index.scss`

## Template: src/theme/vars.scss

```scss
$global-gray-01: #282f37;
$global-gray-02: #343d47;
$global-gray-03: #7d7d7e;
$global-gray-04: #9c9c9e;
$global-gray-05: #d6d6d6;
$global-gray-06: #e4e4e4;
$global-gray-07: #dcdfe2;
$global-gray-08: #f1f3f5;
$global-black: #000;
$global-green-01: #007c49;
$global-green-02: #01ac4c;

// Status Colors
$not-available-color: $global-gray-02;
$in-progress-color: #ff9605;
$picked-color: #277fc1;
$staged-color: #6a3893;
$invoiced-color: $global-green-02;
$inactive-color: $global-gray-03;
$alert-color: #e0281c;
$alert-color-focus: #bc2217;
$alert-color-light: #fadcda;
$success-color: $global-green-02;
$failure-color: $alert-color;

// Background Colors
$global-bg-color-01: #fff;
$global-bg-color-02: #f9f9f9;
$global-bg-color-03: #f2f3f5;
$global-bg-color-04: #f1f1f1;
$global-bg-color-05: #eaebee;
$global-bg-color-06: #f5f5f5;
$global-gray-overlay-01: rgba($global-gray-02, 0.04);
$global-gray-overlay-02: rgba($global-gray-01, 0.2);
$global-white-overlay-01: rgba($global-bg-color-01, 0.7);
$alert-color-overlay: rgba($alert-color, 0.2);
$success-color-overlay: rgba($global-green-02, 0.2);

// Text Colors
$primary-text-color: $global-gray-02;
$secondary-text-color: $global-gray-03;
$regressive-text-color: $global-green-01;
$toggle-button-text-color: #007aff;

// Font Families
$primary-font-family: Roboto, 'Open Sans', Arial, Helvetica, sans-serif;
$title-font-family: 'Roboto Slab', 'Times New Roman', Times, serif;
$caption-font-family: (
  'Roboto Condensed',
  Roboto,
  'Open Sans',
  Arial,
  Helvetica,
  sans-serif
);
$primary-font-size: 0.875rem;

// Standard sizing
$global-border-radius: 0.75rem;
$global-border-radius-progress: 0.125rem;
$global-border-radius-table: 0.5rem;
$global-border-radius-action: 1rem;
$global-border-radius-input: 1.5rem;
$global-icon-size: 1.5rem;
$global-icon-background-size: 2rem;

// Other
$global-backdrop-filter: blur(0.25rem);
$global-transition-time: 0.075s;
$global-transition-time-form: $global-transition-time * 3;
$icon-disabled-opacity: 0.4;
```

## Template: src/theme/shadow.scss

```scss
// Global Shadow styles
@use 'vars.scss' as *;

@mixin global-shadow-0dp {
  box-shadow: none;
}

@mixin global-shadow-2dp {
  box-shadow:
    0 0.125rem 0.25rem 0 rgba($global-black, 0.08),
    0 0.0625rem 0.125rem 0 rgba($global-black, 0.08),
    0 0 0.0625rem 0 rgba($global-black, 0.08);
}

@mixin global-shadow-4dp {
  box-shadow:
    0 0.25rem 0.5rem 0 rgba($global-black, 0.08),
    0 0.125rem 0.25rem 0 rgba($global-black, 0.06),
    0 0 0.125rem 0 rgba($global-black, 0.08);
}

@mixin global-shadow-6dp {
  box-shadow:
    0 0.375rem 0.75rem 0 rgba($global-black, 0.08),
    0 0.1875rem 0.5rem 0 rgba($global-black, 0.04),
    0 0 0.25rem 0 rgba($global-black, 0.08);
}

@mixin global-shadow-8dp {
  box-shadow:
    0 0.5rem 1rem 0 rgba($global-black, 0.12),
    0 0.25rem 0.5rem 0 rgba($global-black, 0.04),
    0 0 0.25rem 0 rgba($global-black, 0.08);
}

@mixin global-shadow-12dp {
  box-shadow:
    0 0.75rem 1.5rem 0 rgba($global-black, 0.12),
    0 0.375rem 0.75rem 0 rgba($global-black, 0.04),
    0 0 0.25rem 0 rgba($global-black, 0.08);
}

@mixin global-shadow-16dp {
  box-shadow:
    0 1rem 2rem 0 rgba($global-black, 0.16),
    0 0.5rem 1rem 0 rgba($global-black, 0.08),
    0 0 0.5rem 0 rgba($global-black, 0.04);
}

@mixin global-shadow-24dp {
  box-shadow:
    0 1.5rem 3rem 0 rgba($global-black, 0.24),
    0 0.75rem 1.5rem 0 rgba($global-black, 0.12),
    0 0 0.75rem 0 rgba($global-black, 0.04);
}

@mixin global-shadow-2dp-top {
  box-shadow:
    0 -0.125rem 0.25rem 0 rgba($global-black, 0.08),
    0 -0.0625rem 0.125rem 0 rgba($global-black, 0.08),
    0 0 0.0625rem 0 rgba($global-black, 0.08);
}

@mixin global-shadow-bottom {
  box-shadow: 0 0.25rem 1.25rem 0 rgba($global-black, 0.28);
}
```

## Template: src/theme/index.scss

```scss
@forward './vars';
```

## Notes
- `vars.scss` provides comprehensive design system variables
- `shadow.scss` provides Material Design-inspired shadow mixins (0dp to 24dp)
- Forward/use pattern enables clean imports in components
- Use with `@use '@/theme/' as *;` in components
- Shadow mixins reference `$global-black` from vars.scss
