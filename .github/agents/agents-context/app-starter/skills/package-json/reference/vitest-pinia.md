# Package.json Reference - Vitest + Pinia Configuration

This reference is used when the user selects:
- **Testing Framework**: Vitest
- **State Management**: Pinia

## Notes

- **State Management**: Uses Pinia with TypeScript composition API support
- **Testing Framework**: Uses Vitest with native ESM support and faster execution
- **Test Configuration**: Requires `vitest.config.ts`
- **Test UI**: Includes `@vitest/ui` for interactive test debugging
- **Store Structure**: `src/stores/` with individual store files (e.g., `useEntityStore.ts`)
- **Pinia Testing**: Includes `@pinia/testing` for easy store mocking in tests
- **No Babel**: Vitest works natively with TypeScript, no Babel configuration needed
- **Coverage**: Uses V8 coverage provider (`@vitest/coverage-v8`)
- **Single-spa integration**: Required for micro frontend architecture
- **Datadog**: Optional, remove if not using monitoring
- **Component Library**: Replace `@royalaholddelhaize/pdl-spectrum-component-library-web` with your own component library or remove
- **Version placeholders**: `{{vue_version}}`, `{{vite_version}}`, `{{typescript_version}}` must be replaced with actual values
