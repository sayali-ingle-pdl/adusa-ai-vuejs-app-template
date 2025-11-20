# Store Skill

## Purpose
Generate the `src/store/index.ts` file with Vuex store configuration.

## Output
Create the file: `src/store/index.ts`

## Template

```typescript
import { createStore } from 'vuex';

export interface RootState {
  version: string;
}

const store = createStore<RootState>({
  state: {
    version: '0.1.0',
  },
  
  modules: {
    auth: {
      namespaced: true,
      state: () => ({
        accessToken: null as string | null,
        user: null,
      }),
      mutations: {
        setAccessToken(state, token: string) {
          state.accessToken = token;
        },
        setUser(state, user: any) {
          state.user = user;
        },
      },
      getters: {
        isAuthenticated(state): boolean {
          return !!state.accessToken;
        },
      },
    },
    
    app: {
      namespaced: true,
      state: () => ({
        queryParams: {} as Record<string, any>,
        loading: false,
        error: null as string | null,
      }),
      mutations: {
        setQueryParams(state, params: Record<string, any>) {
          state.queryParams = params;
        },
        setLoading(state, loading: boolean) {
          state.loading = loading;
        },
        setError(state, error: string | null) {
          state.error = error;
        },
      },
    },
  },
});

export default store;
```

## Notes
- Creates a Vuex store with two modules: auth and app
- Auth module manages access token and user state
- App module manages query params, loading, and error state
- All modules use namespaced: true for cleaner state management
