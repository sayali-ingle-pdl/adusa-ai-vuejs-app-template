# App Component Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains the template and detailed examples.

## Basic Template

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

## App.vue Example with Application ID

```vue
<template>
  <div id="omni-item-manager-web" class="app-content">
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
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0;
  padding: 0;
  scroll-behavior: smooth;

  .app-content {
    margin: 5rem auto 0;
    padding: 0 1.75rem;
    max-width: 100rem;
    z-index: 0;
    position: relative;

    @media only screen and (min-width: 1200px) {
      max-width: 75rem;
    }
  }
}


.no-scroll {
  overflow: hidden;
}

.a11y-visible {
  left: -10000px;
  overflow: hidden;
  position: absolute;
  top: auto;
  width: 1px;
  height: 1px;
}
</style>
```

## Important Notes

- The `id` attribute on the root `div` should use the `appId` constant from `global.ts` constants file
- Replace `omni-item-manager-web` with the actual application ID from the global constants
- The component should be minimal and primarily serve as a container for the router view
- Global styles are defined here for the entire application
