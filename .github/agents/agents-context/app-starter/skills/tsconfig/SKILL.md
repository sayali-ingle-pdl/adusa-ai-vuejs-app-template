# TypeScript Config Skill

## Purpose
Generate the `tsconfig.json` file for TypeScript compilation configuration.

## Output
Create the file: `tsconfig.json`

## Template

```json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "strict": true,
    "jsx": "preserve",
    "moduleResolution": "node",
    "experimentalDecorators": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "useDefineForClassFields": true,
    "sourceMap": true,
    "resolveJsonModule": true,
    "baseUrl": ".",
    "types": ["jest", "vite/client"],
    "paths": {
      "@/*": ["src/*"]
    },
    "lib": ["esnext", "dom", "dom.iterable", "scripthost"]
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "tests/**/*.ts",
    "tests/**/*.tsx",
    "src/shims-svg.d.ts"
  ],
  "exclude": ["node_modules", "src/**/*.spec.ts", "src/**/*.spec.tsx"]
}
```

## Notes
- The `paths` configuration enables `@/` alias for the `src/` directory
- Jest types are included for testing support
- Vite client types are included for import.meta.env support
- Experimental decorators are enabled for Vue class components
