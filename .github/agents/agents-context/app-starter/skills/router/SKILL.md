# Router Skill

## Purpose
Generate the `src/router/index.ts` file with Vue Router configuration and navigation guards.

## Output
Create the file: `src/router/index.ts`

## Template

```typescript
import { createRouter, createWebHashHistory, RouteRecordRaw } from 'vue-router';
import store from '../store';

const routes: Array<RouteRecordRaw> = [
  {
    path: '/',
    name: 'Home',
    component: () => import('../views/Home/Home.vue'),
    meta: { requiresAuth: true },
  },
  {
    path: '/:pathMatch(.*)*',
    name: 'PageNotFound',
    component: () => import('../views/PageNotFoundView/PageNotFoundView.vue'),
  },
];

const router = createRouter({
  history: createWebHashHistory(),
  routes,
});

// Navigation guard to strip and store access token from query params
router.beforeEach((to, _from, next) => {
  if (to.query.accessToken) {
    const token = to.query.accessToken as string;
    store.commit('auth/setAccessToken', token);
    
    // Remove accessToken from URL
    const query = { ...to.query };
    delete query.accessToken;
    next({ ...to, query, replace: true });
  } else {
    next();
  }
});

// Navigation guard to check authentication
router.beforeEach((to, _from, next) => {
  const requiresAuth = to.matched.some(record => record.meta.requiresAuth);
  const isAuthenticated = store.getters['auth/isAuthenticated'];

  if (requiresAuth && !isAuthenticated) {
    // Redirect to login or show error
    console.error('Authentication required');
    next({ name: 'PageNotFound' });
  } else {
    next();
  }
});

// Navigation guard to process query parameters
router.beforeEach((to, _from, next) => {
  if (Object.keys(to.query).length > 0) {
    store.commit('app/setQueryParams', to.query);
  }
  next();
});

export default router;
```

## Notes
- Uses hash-based routing for single-spa compatibility
- Implements three navigation guards:
  1. Strip access token from query params
  2. Check authentication for protected routes
  3. Store query parameters in Vuex
- Lazy loads route components for code splitting
