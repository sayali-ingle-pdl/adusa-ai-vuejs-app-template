# App Component Skill

## Purpose
Generate the `src/App.vue` root component.

## Output
Create the file: `src/App.vue`

## Template

```vue
<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script setup lang="ts">
// Root component - minimal setup
// Router view will render the current route component
</script>

<style lang="scss">
@use '@/theme/' as *;

#app {
  font-family: $font-family-base;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: $color-text-primary;
}
</style>
```

## Notes
- Simple root component with router-view
- Imports theme variables for global styling
- No scoped styles to allow global CSS
