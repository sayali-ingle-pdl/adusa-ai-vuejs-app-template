# TypeScript Configuration Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains the template and detailed examples.

## Basic Template

This skill creates `tsconfig.json` with the following configuration:

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

## Key Features

- **Strict Type Checking**: Enables strict mode for better type safety
- **Path Aliases**: Configures `@/*` alias for `src/*` directory
- **Vue Support**: Includes JSX preserve mode for Vue SFC compilation
- **Test Support**: Includes Jest types and excludes test files from build
- **Source Maps**: Enables source map generation for debugging
- **Module Resolution**: Uses Node.js module resolution strategy
