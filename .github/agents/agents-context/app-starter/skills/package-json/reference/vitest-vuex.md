# Package.json Reference - Vitest + Vuex Configuration

This reference is used when the user selects:
- **Testing Framework**: Vitest
- **State Management**: Vuex

## Notes

- **State Management**: Uses latest stable Vuex with TypeScript support
- **Testing Framework**: Uses Vitest with native ESM support and faster execution
- **Test Configuration**: Requires `vitest.config.ts`
- **Test UI**: Includes `@vitest/ui` for interactive test debugging
- **Store Structure**: `src/store/` with modules in `src/store/modules/`
- **No Babel**: Vitest works natively with TypeScript, no Babel configuration needed
- **Coverage**: Uses V8 coverage provider (`@vitest/coverage-v8`)
- **Single-spa integration**: Required for micro frontend architecture
- **Datadog**: Optional, remove if not using monitoring
- **Component Library**: Replace `@royalaholddelhaize/pdl-spectrum-component-library-web` with your own component library or remove
- **Version placeholders**: `{{vue_version}}`, `{{vite_version}}`, `{{typescript_version}}` must be replaced with actual values
