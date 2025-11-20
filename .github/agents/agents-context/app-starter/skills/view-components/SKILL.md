# View Components Skill

## Purpose
Generate initial view components for the application.

## Output
Create the files:
- `src/views/Home/Home.vue`
- `src/views/PageNotFoundView/PageNotFoundView.vue`

## Template: src/views/Home/Home.vue

```vue
<template>
  <div class="home-view">
    <h1>Welcome to {{ applicationName }}</h1>
    <p>This is the home page of your Vue 3 application.</p>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { appId } from '@/shared/constants/global';

const applicationName = computed(() => {
  return appId.split('-').map(word => 
    word.charAt(0).toUpperCase() + word.slice(1)
  ).join(' ');
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.home-view {
  padding: $spacing-lg;
  
  h1 {
    color: $color-primary;
    margin-bottom: $spacing-md;
  }
  
  p {
    color: $color-text-secondary;
  }
}
</style>
```

## Template: src/views/PageNotFoundView/PageNotFoundView.vue

```vue
<template>
  <div class="page-not-found">
    <h1>404</h1>
    <p>Page Not Found</p>
    <router-link to="/">Go to Home</router-link>
  </div>
</template>

<script setup lang="ts">
// 404 page component
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.page-not-found {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 400px;
  padding: $spacing-lg;
  text-align: center;
  
  h1 {
    font-size: 4rem;
    color: $color-error;
    margin-bottom: $spacing-sm;
  }
  
  p {
    font-size: 1.5rem;
    color: $color-text-secondary;
    margin-bottom: $spacing-md;
  }
  
  a {
    color: $color-primary;
    text-decoration: none;
    
    &:hover {
      text-decoration: underline;
    }
  }
}
</style>
```

## Notes
- Home view displays a welcome message
- PageNotFoundView provides a 404 error page
- Both components use theme variables for styling
- Components are placed in their own directories for organization
