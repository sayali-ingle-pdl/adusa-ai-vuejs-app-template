# Package.json Reference - Jest + Pinia Configuration

This reference is used when the user selects:
- **Testing Framework**: Jest
- **State Management**: Pinia

## Notes

- **State Management**: Uses Pinia with TypeScript composition API support
- **Testing Framework**: Uses latest stable Jest with Vue Test Utils
- **Test Configuration**: Requires `jest.config.cjs`
- **Pinia Testing**: Includes `@pinia/testing` for easy store mocking in tests
- **Store Structure**: `src/stores/` with individual store files (e.g., `useEntityStore.ts`)
- **Single-spa integration**: Required for micro frontend architecture
- **Datadog**: Optional, remove if not using monitoring
- **Component Library**: Replace `@royalaholddelhaize/pdl-spectrum-component-library-web` with your own component library or remove
- **Version placeholders**: `{{vue_version}}`, `{{vite_version}}`, `{{typescript_version}}` must be replaced with actual values
