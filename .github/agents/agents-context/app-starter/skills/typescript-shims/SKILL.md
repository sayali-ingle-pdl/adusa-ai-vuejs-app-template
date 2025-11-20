# TypeScript Shims Skill

## Purpose
Generate TypeScript declaration files for Vue and SVG imports.

## Output
Create the files:
- `src/shims-vue.d.ts`
- `src/shims-svg.d.ts`

## Template: src/shims-vue.d.ts

```typescript
declare module '*.vue' {
  import type { DefineComponent } from 'vue';
  const component: DefineComponent<{}, {}, any>;
  export default component;
}
```

## Template: src/shims-svg.d.ts

```typescript
declare module '*.svg' {
  import type { DefineComponent } from 'vue';
  const component: DefineComponent;
  export default component;
}

declare module '*.svg?url' {
  const content: string;
  export default content;
}
```

## Notes
- shims-vue.d.ts allows importing .vue files in TypeScript
- shims-svg.d.ts allows importing SVG files as Vue components or URLs
- These files enable proper type checking for non-TS imports
