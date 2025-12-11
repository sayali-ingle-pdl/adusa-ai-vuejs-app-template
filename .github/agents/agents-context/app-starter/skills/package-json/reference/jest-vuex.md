# Package.json Reference - Jest + Vuex Configuration

This reference is used when the user selects:
- **Testing Framework**: Jest
- **State Management**: Vuex

## Notes

- **State Management**: Uses latest stable Vuex with TypeScript support
- **Testing Framework**: Uses latest stable Jest with Vue Test Utils
- **Test Configuration**: Requires `jest.config.cjs`
- **Store Structure**: `src/store/` with modules in `src/store/modules/`
- **Single-spa integration**: Required for micro frontend architecture
- **Datadog**: Optional, remove if not using monitoring
- **Component Library**: Replace `@royalaholddelhaize/pdl-spectrum-component-library-web` with your own component library or remove
- **Version placeholders**: `{{vue_version}}`, `{{vite_version}}`, `{{typescript_version}}` must be replaced with actual values
