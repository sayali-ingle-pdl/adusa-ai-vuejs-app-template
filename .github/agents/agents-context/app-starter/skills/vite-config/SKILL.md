# Vite Config Skill

## Purpose
Generate the `vite.config.ts` file for building and serving a Vue 3 application with support for both micro-frontend (single-spa) and standalone architectures.

## Input Parameters
Read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_id`: The application ID for style tag isolation
- `default_port`: The development server port
- `api_base_path`: The base path for API proxy configuration
- `vite_build_format`: The build output format (`system` for micro-frontends, `es` for standalone)

## Output
Create the file: `vite.config.ts`

## Configuration Requirements

### Core Setup

#### Configuration Function
- Use Vite's `defineConfig` function for type-safe configuration
- Accept `mode` and `command` parameters to differentiate between development/production and serve/build
- Return configuration object with conditional logic based on build vs. serve mode

#### Environment Compatibility
- Define `process.env` as empty object for Node.js compatibility in browser environment
- Ensures legacy code expecting `process.env` doesn't break

### Plugins Configuration

#### Required Plugins

**Vue Plugin**
- **Purpose**: Enable Vue 3 single-file component support
- **Package**: `@vitejs/plugin-vue`
- **Configuration**: Use default configuration

**CSS Injection Plugin** (Build Mode Only)
- **Purpose**: Inject CSS via JavaScript for micro-frontend isolation
- **Package**: `vite-plugin-css-injected-by-js`
- **Condition**: Only enable during build, not during development
- **Configuration**: 
  - Use a unique `styleId` derived from your application ID
  - Prevents CSS conflicts in multi-app environments (single-spa)
- **Rationale**: In micro-frontend architectures, CSS must be scoped to prevent style leaking

**SVG Loader Plugin**
- **Purpose**: Import SVG files as Vue components or URLs
- **Package**: `vite-svg-loader`
- **Configuration**:
  - Enable SVGO optimization
  - Preserve viewBox attribute for proper scaling
  - Use preset-default with viewBox override
- **Use Case**: Allows `import IconName from './icon.svg'` to work as Vue component

#### Plugin Array Management
- Filter out falsy values (for conditional plugins)
- Maintain plugin order for proper processing

### Module Resolution

#### Path Aliases
Configure the following aliases:
- **`@/` Alias**: Map to `src/` directory for cleaner imports
- **Vue Resolution**: Explicitly resolve Vue to node_modules to prevent version conflicts in monorepos

#### File Extensions
Support resolution for the following extensions (in order):
- `.mjs` - Modern ES modules
- `.js` - JavaScript files
- `.ts` - TypeScript files
- `.jsx` - JSX files
- `.tsx` - TypeScript JSX files
- `.json` - JSON modules
- `.vue` - Vue single-file components

### Base Path Configuration

#### Development vs. Production
- **Development**: Use root path (`/`) for dev server
- **Production**: Use relative path (`./`) for flexible deployment locations
- **Rationale**: Production builds should work regardless of deployment subdirectory

### Development Server

#### Port Configuration
- Set port using the `default_port` parameter
- Allows multiple micro-frontends to run simultaneously on different ports

#### Proxy Configuration
Configure API proxy for backend service requests:

**Purpose**: Avoid CORS issues during development by proxying API requests

**Configuration Requirements**:
- **Path Pattern**: Use regex pattern starting with `^` followed by your `api_base_path`
- **Target**: Backend service URL (environment-specific)
- **Change Origin**: Enable to rewrite the host header
- **Secure**: Disable for self-signed certificates in development
- **Prepend Path**: Keep original path when proxying

**Example Use Case**: 
- Request to `/api/users` gets proxied to `https://backend.example.com/api/users`
- Solves CORS issues during local development

### Build Configuration

#### Build Target
- Set to `esnext` to leverage modern JavaScript features
- Enables better tree-shaking and smaller bundles
- Assumes modern browser support

#### Library Mode Configuration

**Entry Point**
- Set to `src/main.ts` (application entry file)

**Output Formats**
- Use `vite_build_format` parameter:
  - `system` - For micro-frontends (SystemJS compatible with single-spa)
  - `es` - For standalone SPAs (modern ES modules)

**File Naming**
- Output to `js/app.js` for consistent naming across environments

#### Rollup Options

**External Dependencies**
- For **micro-frontends**: Externalize shared dependencies (Vue, Vue Router, etc.)
- For **standalone apps**: Bundle all dependencies
- Prevents duplicate code when multiple apps share libraries

**Output Configuration**
- Match format with `vite_build_format` parameter
- Set entry file name to `js/app.js`
- Use `auto` exports for flexibility

#### Build Optimization
- **Source Maps**: Enable for debugging in production
- **Empty Output Directory**: Clear `dist/` before each build
- **Target Modern Browsers**: Use esnext for optimal bundle size

### CSS Configuration

#### Preprocessor Options

**SCSS Configuration**
- **Additional Data**: Globally inject theme imports
- **Pattern**: `@use "@/theme/" as *;`
- **Purpose**: Makes theme variables, mixins, and functions available in all components
- **Note**: Use `@use` (modern Sass) rather than `@import` (deprecated)

**Key Benefits**:
- No need to import theme in every component
- Consistent theming across application
- Centralized theme management

## Best Practices

### Micro-Frontend Considerations
When building for single-spa or Module Federation:
- Use `system` format for maximum compatibility
- Externalize shared dependencies to reduce bundle size
- Enable CSS injection via JavaScript for style isolation
- Use unique style IDs to prevent CSS conflicts
- Consider using import maps for shared dependencies

### Standalone Application Considerations
When building a traditional SPA:
- Use `es` format for modern browsers
- Bundle all dependencies for simpler deployment
- CSS can be extracted to separate files
- Simpler build configuration overall

### Development Experience
- Keep dev server port configurable for multi-app environments
- Use proxy for backend API calls to avoid CORS
- Enable hot module replacement (HMR) by default
- Source maps should work in development and production

### Performance Optimization
- Leverage code splitting with dynamic imports
- Use Vite's built-in optimizations (tree-shaking, minification)
- Consider chunk splitting strategies for larger apps
- Monitor bundle size and optimize as needed

## Validation

After generating the configuration:
1. **Dev Server**: Verify the application runs on the specified port
2. **HMR**: Confirm hot module replacement works for Vue components
3. **Alias Resolution**: Test that `@/` imports resolve correctly
4. **SVG Imports**: Verify SVG files can be imported as components
5. **API Proxy**: Check that proxied API requests work without CORS errors
6. **Build Output**: Verify build produces expected format (system/es)
7. **CSS Isolation**: In build mode, confirm CSS is injected via JS with unique ID
8. **Source Maps**: Verify source maps are generated and functional

## Integration Notes

### Single-SPA Integration
For micro-frontends:
- Output format must be `system`
- CSS must be injected via JavaScript
- Shared dependencies should be externalized
- Entry point must export single-spa lifecycle methods

### Theme Integration
- Theme files should exist in `src/theme/` directory
- Use Sass modules (`@use`) for modern syntax
- Export variables, mixins, and functions from theme index

### Environment Variables
- Use Vite's `import.meta.env` for environment variables
- Prefix with `VITE_` to expose to client-side code
- Configure in `.env`, `.env.local`, etc.

### TypeScript Integration
- Configuration file should be TypeScript (`.ts`)
- Use type imports for better IDE support
- Leverage Vite's built-in TypeScript support

## Philosophy

This configuration aims to:
- **Support Multiple Architectures**: Work for both micro-frontends and standalone SPAs
- **Optimize Developer Experience**: Fast HMR, helpful error messages, flexible proxy
- **Production Ready**: Source maps, optimizations, proper module formats
- **Maintainability**: Clear structure, well-documented options, type-safe
- **Flexibility**: Configurable ports, paths, and build formats

## Future Compatibility

When Vite evolves:
- Adopt new plugin APIs as they become available
- Update build optimizations for better performance
- Replace deprecated options with modern equivalents
- Leverage new features like improved CSS handling or module federation
- Maintain compatibility with single-spa and Vue ecosystem
- Keep core principles: fast dev server, optimized builds, flexible configuration

## Common Variations

### Monorepo Setup
- May need to adjust path resolution for shared packages
- Consider workspace protocol for local dependencies
- Coordinate Vite configurations across packages

### Additional Asset Types
Consider adding loaders/plugins for:
- Web fonts
- WebAssembly modules
- Worker scripts
- Progressive Web App assets

### Alternative Build Formats
- **UMD**: For legacy browser support
- **IIFE**: For simple script tag loading
- **CommonJS**: For Node.js compatibility (rare for frontend)

### Environment-Specific Proxy Targets
- Development: Local backend or development environment
- Staging: Staging backend URL
- Production: Production backend URL (if needed)
- Use environment variables to configure dynamically