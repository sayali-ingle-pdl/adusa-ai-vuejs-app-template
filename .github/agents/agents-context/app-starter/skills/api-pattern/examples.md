# Vue API Pattern Examples

This document provides comprehensive examples for both Composition API and Options API patterns.

---

## Table of Contents

1. [Basic Component](#1-basic-component)
2. [Component with Props and Emits](#2-component-with-props-and-emits)
3. [Component with Lifecycle Hooks](#3-component-with-lifecycle-hooks)
4. [Component with Computed Properties](#4-component-with-computed-properties)
5. [Component with Watchers](#5-component-with-watchers)
6. [Component with External Logic (Composables/Mixins)](#6-component-with-external-logic-composablesmixins)
7. [View Component Example](#7-view-component-example)
8. [Component with Store Integration (Pinia)](#8-component-with-store-integration-pinia)
9. [Component with Store Integration (Vuex)](#9-component-with-store-integration-vuex)
10. [Form Component with Validation](#10-form-component-with-validation)

---

## 1. Basic Component

### Composition API

```vue
<template>
  <div class="greeting-card">
    <h2>{{ title }}</h2>
    <p>{{ message }}</p>
    <button @click="updateMessage">Update</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const title = ref('Welcome');
const message = ref('Hello from Composition API');

function updateMessage() {
  message.value = 'Message updated!';
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.greeting-card {
  padding: var(--spacing-md);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
}
</style>
```

### Options API

```vue
<template>
  <div class="greeting-card">
    <h2>{{ title }}</h2>
    <p>{{ message }}</p>
    <button @click="updateMessage">Update</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'GreetingCard',
  
  data() {
    return {
      title: 'Welcome',
      message: 'Hello from Options API'
    };
  },
  
  methods: {
    updateMessage() {
      this.message = 'Message updated!';
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.greeting-card {
  padding: var(--spacing-md);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
}
</style>
```

---

## 2. Component with Props and Emits

### Composition API

```vue
<template>
  <div class="user-card">
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
    <button @click="handleEdit">Edit</button>
    <button @click="handleDelete">Delete</button>
  </div>
</template>

<script setup lang="ts">
import type { User } from '@/interfaces/User';

interface Props {
  user: User;
  readonly?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  readonly: false
});

const emit = defineEmits<{
  edit: [userId: string];
  delete: [userId: string];
}>();

function handleEdit() {
  if (!props.readonly) {
    emit('edit', props.user.id);
  }
}

function handleDelete() {
  if (!props.readonly) {
    emit('delete', props.user.id);
  }
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.user-card {
  padding: var(--spacing-md);
  background: var(--surface-color);
  border-radius: var(--border-radius);
}
</style>
```

### Options API

```vue
<template>
  <div class="user-card">
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
    <button @click="handleEdit">Edit</button>
    <button @click="handleDelete">Delete</button>
  </div>
</template>

<script lang="ts">
import { defineComponent, type PropType } from 'vue';
import type { User } from '@/interfaces/User';

export default defineComponent({
  name: 'UserCard',
  
  props: {
    user: {
      type: Object as PropType<User>,
      required: true
    },
    readonly: {
      type: Boolean,
      default: false
    }
  },
  
  emits: {
    edit: (userId: string) => typeof userId === 'string',
    delete: (userId: string) => typeof userId === 'string'
  },
  
  methods: {
    handleEdit() {
      if (!this.readonly) {
        this.$emit('edit', this.user.id);
      }
    },
    
    handleDelete() {
      if (!this.readonly) {
        this.$emit('delete', this.user.id);
      }
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.user-card {
  padding: var(--spacing-md);
  background: var(--surface-color);
  border-radius: var(--border-radius);
}
</style>
```

---

## 3. Component with Lifecycle Hooks

### Composition API

```vue
<template>
  <div class="data-fetcher">
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">Error: {{ error }}</div>
    <div v-else>
      <ul>
        <li v-for="item in items" :key="item.id">{{ item.name }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { dataService } from '@/services/dataService';
import type { DataItem } from '@/interfaces/DataItem';

const items = ref<DataItem[]>([]);
const loading = ref(false);
const error = ref<string | null>(null);
let intervalId: number | null = null;

async function fetchData() {
  loading.value = true;
  error.value = null;
  
  try {
    items.value = await dataService.getAll();
  } catch (err) {
    error.value = err instanceof Error ? err.message : 'Unknown error';
  } finally {
    loading.value = false;
  }
}

onMounted(() => {
  fetchData();
  intervalId = window.setInterval(fetchData, 30000);
});

onBeforeUnmount(() => {
  if (intervalId !== null) {
    clearInterval(intervalId);
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.data-fetcher {
  padding: var(--spacing-md);
}
</style>
```

### Options API

```vue
<template>
  <div class="data-fetcher">
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">Error: {{ error }}</div>
    <div v-else>
      <ul>
        <li v-for="item in items" :key="item.id">{{ item.name }}</li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { dataService } from '@/services/dataService';
import type { DataItem } from '@/interfaces/DataItem';

export default defineComponent({
  name: 'DataFetcher',
  
  data() {
    return {
      items: [] as DataItem[],
      loading: false,
      error: null as string | null,
      intervalId: null as number | null
    };
  },
  
  methods: {
    async fetchData() {
      this.loading = true;
      this.error = null;
      
      try {
        this.items = await dataService.getAll();
      } catch (err) {
        this.error = err instanceof Error ? err.message : 'Unknown error';
      } finally {
        this.loading = false;
      }
    }
  },
  
  mounted() {
    this.fetchData();
    this.intervalId = window.setInterval(this.fetchData, 30000);
  },
  
  beforeUnmount() {
    if (this.intervalId !== null) {
      clearInterval(this.intervalId);
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.data-fetcher {
  padding: var(--spacing-md);
}
</style>
```

---

## 4. Component with Computed Properties

### Composition API

```vue
<template>
  <div class="shopping-cart">
    <h2>Shopping Cart ({{ itemCount }} items)</h2>
    <ul>
      <li v-for="item in cart" :key="item.id">
        {{ item.name }} - ${{ item.price }} x {{ item.quantity }}
      </li>
    </ul>
    <div class="total">
      <strong>Subtotal:</strong> ${{ subtotal.toFixed(2) }}
    </div>
    <div class="total">
      <strong>Tax ({{ taxRate * 100 }}%):</strong> ${{ tax.toFixed(2) }}
    </div>
    <div class="total">
      <strong>Total:</strong> ${{ total.toFixed(2) }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import type { CartItem } from '@/interfaces/CartItem';

const cart = ref<CartItem[]>([
  { id: '1', name: 'Product A', price: 29.99, quantity: 2 },
  { id: '2', name: 'Product B', price: 49.99, quantity: 1 }
]);

const taxRate = ref(0.08);

const itemCount = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.quantity, 0);
});

const subtotal = computed(() => {
  return cart.value.reduce((sum, item) => sum + (item.price * item.quantity), 0);
});

const tax = computed(() => {
  return subtotal.value * taxRate.value;
});

const total = computed(() => {
  return subtotal.value + tax.value;
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.shopping-cart {
  padding: var(--spacing-md);
  
  .total {
    margin-top: var(--spacing-sm);
    font-size: 1.1rem;
  }
}
</style>
```

### Options API

```vue
<template>
  <div class="shopping-cart">
    <h2>Shopping Cart ({{ itemCount }} items)</h2>
    <ul>
      <li v-for="item in cart" :key="item.id">
        {{ item.name }} - ${{ item.price }} x {{ item.quantity }}
      </li>
    </ul>
    <div class="total">
      <strong>Subtotal:</strong> ${{ subtotal.toFixed(2) }}
    </div>
    <div class="total">
      <strong>Tax ({{ taxRate * 100 }}%):</strong> ${{ tax.toFixed(2) }}
    </div>
    <div class="total">
      <strong>Total:</strong> ${{ total.toFixed(2) }}
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import type { CartItem } from '@/interfaces/CartItem';

export default defineComponent({
  name: 'ShoppingCart',
  
  data() {
    return {
      cart: [
        { id: '1', name: 'Product A', price: 29.99, quantity: 2 },
        { id: '2', name: 'Product B', price: 49.99, quantity: 1 }
      ] as CartItem[],
      taxRate: 0.08
    };
  },
  
  computed: {
    itemCount(): number {
      return this.cart.reduce((sum, item) => sum + item.quantity, 0);
    },
    
    subtotal(): number {
      return this.cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    },
    
    tax(): number {
      return this.subtotal * this.taxRate;
    },
    
    total(): number {
      return this.subtotal + this.tax;
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.shopping-cart {
  padding: var(--spacing-md);
  
  .total {
    margin-top: var(--spacing-sm);
    font-size: 1.1rem;
  }
}
</style>
```

---

## 5. Component with Watchers

### Composition API

```vue
<template>
  <div class="search-box">
    <input 
      v-model="searchQuery" 
      placeholder="Search..."
      @input="handleInput"
    />
    <div v-if="searching">Searching...</div>
    <ul v-else>
      <li v-for="result in results" :key="result.id">{{ result.name }}</li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';
import { debounce } from '@/shared/utils/debounce';
import { searchService } from '@/services/searchService';
import type { SearchResult } from '@/interfaces/SearchResult';

const searchQuery = ref('');
const results = ref<SearchResult[]>([]);
const searching = ref(false);

async function performSearch(query: string) {
  if (!query.trim()) {
    results.value = [];
    return;
  }
  
  searching.value = true;
  try {
    results.value = await searchService.search(query);
  } finally {
    searching.value = false;
  }
}

const debouncedSearch = debounce(performSearch, 300);

watch(searchQuery, (newQuery) => {
  debouncedSearch(newQuery);
});

function handleInput() {
  // Additional input handling if needed
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.search-box {
  padding: var(--spacing-md);
  
  input {
    width: 100%;
    padding: var(--spacing-sm);
    border: 1px solid var(--border-color);
    border-radius: var(--border-radius);
  }
}
</style>
```

### Options API

```vue
<template>
  <div class="search-box">
    <input 
      v-model="searchQuery" 
      placeholder="Search..."
      @input="handleInput"
    />
    <div v-if="searching">Searching...</div>
    <ul v-else>
      <li v-for="result in results" :key="result.id">{{ result.name }}</li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { debounce } from '@/shared/utils/debounce';
import { searchService } from '@/services/searchService';
import type { SearchResult } from '@/interfaces/SearchResult';

export default defineComponent({
  name: 'SearchBox',
  
  data() {
    return {
      searchQuery: '',
      results: [] as SearchResult[],
      searching: false
    };
  },
  
  watch: {
    searchQuery(newQuery: string) {
      this.debouncedSearch(newQuery);
    }
  },
  
  methods: {
    async performSearch(query: string) {
      if (!query.trim()) {
        this.results = [];
        return;
      }
      
      this.searching = true;
      try {
        this.results = await searchService.search(query);
      } finally {
        this.searching = false;
      }
    },
    
    handleInput() {
      // Additional input handling if needed
    }
  },
  
  created() {
    this.debouncedSearch = debounce(this.performSearch, 300);
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.search-box {
  padding: var(--spacing-md);
  
  input {
    width: 100%;
    padding: var(--spacing-sm);
    border: 1px solid var(--border-color);
    border-radius: var(--border-radius);
  }
}
</style>
```

---

## 6. Component with External Logic (Composables/Mixins)

### Composition API (with Composable)

**Composable: `src/composables/useAuth.ts`**
```typescript
import { ref, computed } from 'vue';
import type { User } from '@/interfaces/User';

export function useAuth() {
  const user = ref<User | null>(null);
  const loading = ref(false);
  
  const isAuthenticated = computed(() => user.value !== null);
  
  async function login(username: string, password: string) {
    loading.value = true;
    try {
      // Login logic
      user.value = { id: '1', name: username, email: `${username}@example.com` };
    } finally {
      loading.value = false;
    }
  }
  
  function logout() {
    user.value = null;
  }
  
  return {
    user,
    loading,
    isAuthenticated,
    login,
    logout
  };
}
```

**Component:**
```vue
<template>
  <div class="auth-status">
    <div v-if="isAuthenticated">
      <p>Welcome, {{ user?.name }}</p>
      <button @click="logout">Logout</button>
    </div>
    <div v-else>
      <button @click="showLogin">Login</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useAuth } from '@/composables/useAuth';

const { user, isAuthenticated, logout } = useAuth();

function showLogin() {
  // Show login dialog
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.auth-status {
  padding: var(--spacing-md);
}
</style>
```

### Options API (with Mixin)

**Mixin: `src/mixins/authMixin.ts`**
```typescript
import { defineComponent } from 'vue';
import type { User } from '@/interfaces/User';

export const authMixin = defineComponent({
  data() {
    return {
      user: null as User | null,
      loading: false
    };
  },
  
  computed: {
    isAuthenticated(): boolean {
      return this.user !== null;
    }
  },
  
  methods: {
    async login(username: string, password: string) {
      this.loading = true;
      try {
        // Login logic
        this.user = { id: '1', name: username, email: `${username}@example.com` };
      } finally {
        this.loading = false;
      }
    },
    
    logout() {
      this.user = null;
    }
  }
});
```

**Component:**
```vue
<template>
  <div class="auth-status">
    <div v-if="isAuthenticated">
      <p>Welcome, {{ user?.name }}</p>
      <button @click="logout">Logout</button>
    </div>
    <div v-else>
      <button @click="showLogin">Login</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { authMixin } from '@/mixins/authMixin';

export default defineComponent({
  name: 'AuthStatus',
  
  mixins: [authMixin],
  
  methods: {
    showLogin() {
      // Show login dialog
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.auth-status {
  padding: var(--spacing-md);
}
</style>
```

---

## 7. View Component Example

### Composition API

```vue
<template>
  <div class="inventory-view">
    <header class="view-header">
      <h1>Inventory Management</h1>
      <button @click="handleAddNew">Add New Item</button>
    </header>
    
    <SearchBar 
      v-model="searchQuery"
      @search="handleSearch"
    />
    
    <div v-if="loading" class="loading">Loading inventory...</div>
    <div v-else-if="error" class="error">{{ error }}</div>
    <div v-else>
      <InventoryList 
        :items="filteredItems"
        @edit="handleEdit"
        @delete="handleDelete"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import SearchBar from '@/components/common/SearchBar.vue';
import InventoryList from '@/components/inventory/InventoryList.vue';
import { inventoryService } from '@/services/inventoryService';
import type { InventoryItem } from '@/interfaces/InventoryItem';

const router = useRouter();
const items = ref<InventoryItem[]>([]);
const searchQuery = ref('');
const loading = ref(false);
const error = ref<string | null>(null);

const filteredItems = computed(() => {
  if (!searchQuery.value) return items.value;
  
  return items.value.filter(item => 
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

async function loadItems() {
  loading.value = true;
  error.value = null;
  
  try {
    items.value = await inventoryService.getAll();
  } catch (err) {
    error.value = err instanceof Error ? err.message : 'Failed to load inventory';
  } finally {
    loading.value = false;
  }
}

function handleSearch(query: string) {
  searchQuery.value = query;
}

function handleAddNew() {
  router.push('/inventory/new');
}

function handleEdit(itemId: string) {
  router.push(`/inventory/edit/${itemId}`);
}

async function handleDelete(itemId: string) {
  if (confirm('Are you sure you want to delete this item?')) {
    try {
      await inventoryService.delete(itemId);
      await loadItems();
    } catch (err) {
      error.value = err instanceof Error ? err.message : 'Failed to delete item';
    }
  }
}

onMounted(() => {
  loadItems();
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.inventory-view {
  padding: var(--spacing-lg);
  
  .view-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: var(--spacing-lg);
  }
  
  .loading,
  .error {
    padding: var(--spacing-md);
    text-align: center;
  }
  
  .error {
    color: var(--error-color);
  }
}
</style>
```

### Options API

```vue
<template>
  <div class="inventory-view">
    <header class="view-header">
      <h1>Inventory Management</h1>
      <button @click="handleAddNew">Add New Item</button>
    </header>
    
    <SearchBar 
      v-model="searchQuery"
      @search="handleSearch"
    />
    
    <div v-if="loading" class="loading">Loading inventory...</div>
    <div v-else-if="error" class="error">{{ error }}</div>
    <div v-else>
      <InventoryList 
        :items="filteredItems"
        @edit="handleEdit"
        @delete="handleDelete"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import SearchBar from '@/components/common/SearchBar.vue';
import InventoryList from '@/components/inventory/InventoryList.vue';
import { inventoryService } from '@/services/inventoryService';
import type { InventoryItem } from '@/interfaces/InventoryItem';

export default defineComponent({
  name: 'InventoryView',
  
  components: {
    SearchBar,
    InventoryList
  },
  
  data() {
    return {
      items: [] as InventoryItem[],
      searchQuery: '',
      loading: false,
      error: null as string | null
    };
  },
  
  computed: {
    filteredItems(): InventoryItem[] {
      if (!this.searchQuery) return this.items;
      
      return this.items.filter(item => 
        item.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    }
  },
  
  methods: {
    async loadItems() {
      this.loading = true;
      this.error = null;
      
      try {
        this.items = await inventoryService.getAll();
      } catch (err) {
        this.error = err instanceof Error ? err.message : 'Failed to load inventory';
      } finally {
        this.loading = false;
      }
    },
    
    handleSearch(query: string) {
      this.searchQuery = query;
    },
    
    handleAddNew() {
      this.$router.push('/inventory/new');
    },
    
    handleEdit(itemId: string) {
      this.$router.push(`/inventory/edit/${itemId}`);
    },
    
    async handleDelete(itemId: string) {
      if (confirm('Are you sure you want to delete this item?')) {
        try {
          await inventoryService.delete(itemId);
          await this.loadItems();
        } catch (err) {
          this.error = err instanceof Error ? err.message : 'Failed to delete item';
        }
      }
    }
  },
  
  mounted() {
    this.loadItems();
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.inventory-view {
  padding: var(--spacing-lg);
  
  .view-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: var(--spacing-lg);
  }
  
  .loading,
  .error {
    padding: var(--spacing-md);
    text-align: center;
  }
  
  .error {
    color: var(--error-color);
  }
}
</style>
```

---

## 8. Component with Store Integration (Pinia)

### Composition API

```vue
<template>
  <div class="user-profile">
    <div v-if="loading">Loading profile...</div>
    <div v-else-if="currentUser">
      <h2>{{ currentUser.name }}</h2>
      <p>{{ currentUser.email }}</p>
      <button @click="handleUpdateProfile">Update Profile</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { useUserStore } from '@/stores/userStore';

const userStore = useUserStore();

const currentUser = computed(() => userStore.currentUser);
const loading = computed(() => userStore.loading);

async function handleUpdateProfile() {
  await userStore.updateProfile({
    name: 'Updated Name',
    email: currentUser.value?.email || ''
  });
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.user-profile {
  padding: var(--spacing-md);
}
</style>
```

### Options API

```vue
<template>
  <div class="user-profile">
    <div v-if="loading">Loading profile...</div>
    <div v-else-if="currentUser">
      <h2>{{ currentUser.name }}</h2>
      <p>{{ currentUser.email }}</p>
      <button @click="handleUpdateProfile">Update Profile</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { mapState, mapActions } from 'pinia';
import { useUserStore } from '@/stores/userStore';

export default defineComponent({
  name: 'UserProfile',
  
  computed: {
    ...mapState(useUserStore, ['currentUser', 'loading'])
  },
  
  methods: {
    ...mapActions(useUserStore, ['updateProfile']),
    
    async handleUpdateProfile() {
      await this.updateProfile({
        name: 'Updated Name',
        email: this.currentUser?.email || ''
      });
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.user-profile {
  padding: var(--spacing-md);
}
</style>
```

---

## 9. Component with Store Integration (Vuex)

### Composition API

```vue
<template>
  <div class="user-profile">
    <div v-if="loading">Loading profile...</div>
    <div v-else-if="currentUser">
      <h2>{{ currentUser.name }}</h2>
      <p>{{ currentUser.email }}</p>
      <button @click="handleUpdateProfile">Update Profile</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { useStore } from 'vuex';
import type { RootState } from '@/store';

const store = useStore<RootState>();

const currentUser = computed(() => store.state.user.currentUser);
const loading = computed(() => store.state.user.loading);

async function handleUpdateProfile() {
  await store.dispatch('user/updateProfile', {
    name: 'Updated Name',
    email: currentUser.value?.email || ''
  });
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.user-profile {
  padding: var(--spacing-md);
}
</style>
```

### Options API

```vue
<template>
  <div class="user-profile">
    <div v-if="loading">Loading profile...</div>
    <div v-else-if="currentUser">
      <h2>{{ currentUser.name }}</h2>
      <p>{{ currentUser.email }}</p>
      <button @click="handleUpdateProfile">Update Profile</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { mapState, mapActions } from 'vuex';

export default defineComponent({
  name: 'UserProfile',
  
  computed: {
    ...mapState('user', ['currentUser', 'loading'])
  },
  
  methods: {
    ...mapActions('user', ['updateProfile']),
    
    async handleUpdateProfile() {
      await this.updateProfile({
        name: 'Updated Name',
        email: this.currentUser?.email || ''
      });
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.user-profile {
  padding: var(--spacing-md);
}
</style>
```

---

## 10. Form Component with Validation

### Composition API

```vue
<template>
  <form class="registration-form" @submit.prevent="handleSubmit">
    <div class="form-group">
      <label for="username">Username</label>
      <input 
        id="username"
        v-model="form.username"
        type="text"
        @blur="validateUsername"
      />
      <span v-if="errors.username" class="error">{{ errors.username }}</span>
    </div>
    
    <div class="form-group">
      <label for="email">Email</label>
      <input 
        id="email"
        v-model="form.email"
        type="email"
        @blur="validateEmail"
      />
      <span v-if="errors.email" class="error">{{ errors.email }}</span>
    </div>
    
    <div class="form-group">
      <label for="password">Password</label>
      <input 
        id="password"
        v-model="form.password"
        type="password"
        @blur="validatePassword"
      />
      <span v-if="errors.password" class="error">{{ errors.password }}</span>
    </div>
    
    <button type="submit" :disabled="!isFormValid || submitting">
      {{ submitting ? 'Submitting...' : 'Register' }}
    </button>
  </form>
</template>

<script setup lang="ts">
import { ref, computed, reactive } from 'vue';
import { useRouter } from 'vue-router';
import { authService } from '@/services/authService';

const router = useRouter();

const form = reactive({
  username: '',
  email: '',
  password: ''
});

const errors = reactive({
  username: '',
  email: '',
  password: ''
});

const submitting = ref(false);

const isFormValid = computed(() => {
  return form.username && form.email && form.password &&
         !errors.username && !errors.email && !errors.password;
});

function validateUsername() {
  if (!form.username) {
    errors.username = 'Username is required';
  } else if (form.username.length < 3) {
    errors.username = 'Username must be at least 3 characters';
  } else {
    errors.username = '';
  }
}

function validateEmail() {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!form.email) {
    errors.email = 'Email is required';
  } else if (!emailRegex.test(form.email)) {
    errors.email = 'Invalid email format';
  } else {
    errors.email = '';
  }
}

function validatePassword() {
  if (!form.password) {
    errors.password = 'Password is required';
  } else if (form.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  } else {
    errors.password = '';
  }
}

async function handleSubmit() {
  validateUsername();
  validateEmail();
  validatePassword();
  
  if (!isFormValid.value) return;
  
  submitting.value = true;
  try {
    await authService.register(form);
    router.push('/login');
  } catch (err) {
    console.error('Registration failed:', err);
  } finally {
    submitting.value = false;
  }
}
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.registration-form {
  max-width: 400px;
  padding: var(--spacing-lg);
  
  .form-group {
    margin-bottom: var(--spacing-md);
    
    label {
      display: block;
      margin-bottom: var(--spacing-xs);
      font-weight: 600;
    }
    
    input {
      width: 100%;
      padding: var(--spacing-sm);
      border: 1px solid var(--border-color);
      border-radius: var(--border-radius);
    }
    
    .error {
      display: block;
      margin-top: var(--spacing-xs);
      color: var(--error-color);
      font-size: 0.875rem;
    }
  }
  
  button {
    width: 100%;
    padding: var(--spacing-sm);
    background: var(--primary-color);
    color: white;
    border: none;
    border-radius: var(--border-radius);
    cursor: pointer;
    
    &:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }
  }
}
</style>
```

### Options API

```vue
<template>
  <form class="registration-form" @submit.prevent="handleSubmit">
    <div class="form-group">
      <label for="username">Username</label>
      <input 
        id="username"
        v-model="form.username"
        type="text"
        @blur="validateUsername"
      />
      <span v-if="errors.username" class="error">{{ errors.username }}</span>
    </div>
    
    <div class="form-group">
      <label for="email">Email</label>
      <input 
        id="email"
        v-model="form.email"
        type="email"
        @blur="validateEmail"
      />
      <span v-if="errors.email" class="error">{{ errors.email }}</span>
    </div>
    
    <div class="form-group">
      <label for="password">Password</label>
      <input 
        id="password"
        v-model="form.password"
        type="password"
        @blur="validatePassword"
      />
      <span v-if="errors.password" class="error">{{ errors.password }}</span>
    </div>
    
    <button type="submit" :disabled="!isFormValid || submitting">
      {{ submitting ? 'Submitting...' : 'Register' }}
    </button>
  </form>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { authService } from '@/services/authService';

export default defineComponent({
  name: 'RegistrationForm',
  
  data() {
    return {
      form: {
        username: '',
        email: '',
        password: ''
      },
      errors: {
        username: '',
        email: '',
        password: ''
      },
      submitting: false
    };
  },
  
  computed: {
    isFormValid(): boolean {
      return this.form.username && this.form.email && this.form.password &&
             !this.errors.username && !this.errors.email && !this.errors.password;
    }
  },
  
  methods: {
    validateUsername() {
      if (!this.form.username) {
        this.errors.username = 'Username is required';
      } else if (this.form.username.length < 3) {
        this.errors.username = 'Username must be at least 3 characters';
      } else {
        this.errors.username = '';
      }
    },
    
    validateEmail() {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!this.form.email) {
        this.errors.email = 'Email is required';
      } else if (!emailRegex.test(this.form.email)) {
        this.errors.email = 'Invalid email format';
      } else {
        this.errors.email = '';
      }
    },
    
    validatePassword() {
      if (!this.form.password) {
        this.errors.password = 'Password is required';
      } else if (this.form.password.length < 8) {
        this.errors.password = 'Password must be at least 8 characters';
      } else {
        this.errors.password = '';
      }
    },
    
    async handleSubmit() {
      this.validateUsername();
      this.validateEmail();
      this.validatePassword();
      
      if (!this.isFormValid) return;
      
      this.submitting = true;
      try {
        await authService.register(this.form);
        this.$router.push('/login');
      } catch (err) {
        console.error('Registration failed:', err);
      } finally {
        this.submitting = false;
      }
    }
  }
});
</script>

<style scoped lang="scss">
@use '@/theme/' as *;

.registration-form {
  max-width: 400px;
  padding: var(--spacing-lg);
  
  .form-group {
    margin-bottom: var(--spacing-md);
    
    label {
      display: block;
      margin-bottom: var(--spacing-xs);
      font-weight: 600;
    }
    
    input {
      width: 100%;
      padding: var(--spacing-sm);
      border: 1px solid var(--border-color);
      border-radius: var(--border-radius);
    }
    
    .error {
      display: block;
      margin-top: var(--spacing-xs);
      color: var(--error-color);
      font-size: 0.875rem;
    }
  }
  
  button {
    width: 100%;
    padding: var(--spacing-sm);
    background: var(--primary-color);
    color: white;
    border: none;
    border-radius: var(--border-radius);
    cursor: pointer;
    
    &:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }
  }
}
</style>
```

---

## Summary

These examples demonstrate the key patterns for both Composition API and Options API approaches. When generating Vue components:

1. **Choose the pattern** based on user selection (`composition-api` or `options-api`)
2. **Follow TypeScript conventions** - Always use `lang="ts"` in script tags
3. **Use scoped styles** - Always include `scoped lang="scss"` in style tags
4. **Import theme variables** - Use `@use '@/theme/' as *;` in styles
5. **Type everything** - Define interfaces for props, emits, and data structures
6. **Follow naming conventions** - PascalCase for component names and files
7. **Include proper error handling** - Try-catch blocks and error states
8. **Add loading states** - For async operations

The Composition API is recommended for new Vue 3 projects due to better TypeScript support, improved code reusability, and better tree-shaking. The Options API is still fully supported and may be preferred for teams transitioning from Vue 2 or those who prefer the object-based structure.
