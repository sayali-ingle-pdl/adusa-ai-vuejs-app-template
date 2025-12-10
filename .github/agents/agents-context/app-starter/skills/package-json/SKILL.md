# Package JSON Skill

## Purpose
Generate the `package.json` file for a Vue 3 Vite application with all necessary dependencies, dev dependencies, and scripts.

## Instructions

1. **Gather Input Parameters** from user (see Input Parameters section below)
2. **Check version strategy**: If `use_latest_versions` is true, use "Version Configuration" below; otherwise use pinned versions
3. **Load versions**: For "latest" entries, fetch from npm: `npm view <package> version`
4. **Select Template**: Choose appropriate template from `reference/` directory based on user choices
   - If `use_latest_versions === true`: Use `reference/latest-versions.md`
   - Otherwise: Select based on `test_framework` and `state_management`
5. **Generate** package.json using the selected template
6. **Validate** output meets all requirements
7. **Create** the file at project root: `package.json`

## Input Parameters

Ask the user or read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_name`: The application name in kebab-case
- `project_scope`: The NPM scope/organization
- `default_port`: The development server port
- `use_latest_versions`: Whether to use latest versions from npm (`true` or `false`) - **REQUIRED**
- `test_framework`: Testing framework (`jest` or `vitest`) - **Only if `use_latest_versions === false`**
- `state_management`: State management library (`vuex` or `pinia`) - **Only if `use_latest_versions === false`**

**Version Strategy**:
- If `use_latest_versions === true`: Use "Version Configuration" section below and fetch from npm
  - Automatically uses: Vitest (test framework) + Pinia (state management) - latest recommendations
- If `use_latest_versions === false`: Use pinned versions from selected template
  - User chooses: test_framework and state_management

When `use_latest_versions` is true:
- Fetch from npm: `npm view <package> version`
- Apply caret prefix: `3.5.13` → `^3.5.13`
- Use template: `reference/latest-versions.md`
- Use recommended tools: Vitest + Pinia

When `use_latest_versions` is false:
- User selects test framework (jest/vitest) and state management (vuex/pinia)
- Use pinned versions from template (e.g., `reference/jest-vuex.md`)
- No npm fetch needed

## Template Reference Selection

Based on `use_latest_versions`, `test_framework` and `state_management`, use the appropriate template from `reference/`:

**If `use_latest_versions === true`**:
- Use: `reference/latest-versions.md` (fetches latest versions from npm)
- Automatically sets: `test_framework = "vitest"` and `state_management = "pinia"`
- These are the latest recommended tools

**If `use_latest_versions === false`**:
- Select template based on user's choices:
  - **Jest + Vuex**: `reference/jest-vuex.md`
  - **Jest + Pinia**: `reference/jest-pinia.md`
  - **Vitest + Vuex**: `reference/vitest-vuex.md`
  - **Vitest + Pinia**: `reference/vitest-pinia.md`

## Output
- File: `package.json` at project root
- Format: Valid JSON with proper indentation (2 spaces)

## Template Structure

```json
{
  "name": "{{project_scope}}/{{application_name}}",
  "version": "0.1.0",
  "private": true,
  "type": "module",
  "scripts": {
    // See reference.md for complete scripts list
  },
  "dependencies": {
    // See reference.md for complete dependencies
  },
  "devDependencies": {
    // See reference.md for complete devDependencies
  }
}
```

## Validation
- All version placeholders are replaced with actual values
- Package name follows format `@scope/name`
- `type` field is set to `"module"`
- All required scripts are defined
- Valid JSON syntax

## Version Management

### Using Latest Versions
When the user asks for the **latest configuration**, the skill should:

1. **Load versions.json**:
   ```javascript
   const versionsConfig = require('../../versions.json');
   ```

2. **Resolve "latest" entries**:
   - For packages marked as `"latest"` in versions.json
   - Use the version-resolver skill to fetch from npm registry
   - Example: `"vue": "latest"` → `"vue": "^3.5.13"`

3. **Apply resolved versions**:
   ```json
   {
     "dependencies": {
       "vue": "{{vue_version}}",           // Resolved from versions.json
       "vue-router": "{{vue_router_version}}",
       "vuex": "{{vuex_version}}"
     }
   }
   ```

### Version Resolution Examples

**Example 1: All Latest**
```json
// versions.json
{
  "core": {
    "vue": "latest",
    "vite": "latest"
  },
  "dependencies": {
    "vue-router": "latest",
    "axios": "latest"
  }
}

// Result after resolution
{
  "dependencies": {
    "vue": "^3.5.13",
    "vue-router": "^4.6.3",
    "axios": "^1.7.9"
  },
  "devDependencies": {
    "vite": "^6.3.5"
  }
}
```

**Example 2: Mixed (Latest + Pinned)**
```json
// versions.json
{
  "core": {
    "vue": "latest",
    "typescript": "^5.7.3"
  }
}

// Result after resolution
{
  "dependencies": {
    "vue": "^3.5.13"  // Fetched from npm
  },
  "devDependencies": {
    "typescript": "^5.7.3"  // Used as-is
  }
}
```

### User Request Handling

## User Request Handling

**When user says**: "Generate package.json with latest versions" OR `use_latest_versions === true`
**Action**:
1. Use Version Configuration section below from this SKILL.md
2. For all entries: Run `npm view <package> version` to fetch current version
3. Apply caret prefix: `3.5.13` → `^3.5.13`
4. Use template: `reference/latest-versions.md`
5. Generate package.json with resolved versions

**When user says**: "Use stable/tested versions" OR `use_latest_versions === false`
**Action**:
1. Select appropriate template from `reference/` based on test_framework and state_management
2. Use pinned versions from selected template (no npm fetch needed)
3. Generate package.json with specified versions

## Version Configuration (versions.json)

This is the central version configuration embedded in this SKILL.md.

Use this configuration to determine which versions to use:

```json
{
  "core": {
    "node": "latest",
    "vue": "latest",
    "vite": "latest",
    "typescript": "latest"
  },
  
  "dependencies": {
    "vue-router": "latest",
    "vuex": "latest",
    "pinia": "latest",
    "axios": "latest",
    "core-js": "latest",
    "single-spa-vue": "latest",
    "vue-class-component": "latest",
    "vue-tippy": "latest",
    "date-fns": "latest",
    "date-fns-tz": "latest",
    "@datadog/browser-rum": "latest"
  },
  
  "devDependencies": {
    "@vitejs/plugin-vue": "latest",
    "@types/node": "latest",
    "@types/jest": "latest",
    "@types/jsdom": "latest",
    "vite-plugin-css-injected-by-js": "latest",
    "vite-svg-loader": "latest",
    "vitest": "latest",
    "@vitest/ui": "latest",
    "jest": "latest",
    "@vue/test-utils": "latest",
    "@vue/vue3-jest": "latest",
    "babel-jest": "latest",
    "ts-jest": "latest",
    "@babel/core": "latest",
    "@babel/plugin-transform-runtime": "latest",
    "@babel/preset-env": "latest",
    "eslint": "latest",
    "eslint-plugin-vue": "latest",
    "@typescript-eslint/eslint-plugin": "latest",
    "@typescript-eslint/parser": "latest",
    "vue-eslint-parser": "latest",
    "@vue/eslint-config-typescript": "latest",
    "eslint-config-prettier": "latest",
    "eslint-plugin-prettier": "latest",
    "@eslint/js": "latest",
    "@eslint/eslintrc": "latest",
    "prettier": "latest",
    "sass": "latest",
    "sass-loader": "latest",
    "stylelint": "latest",
    "stylelint-config-recommended-vue": "latest",
    "stylelint-config-standard-scss": "latest",
    "husky": "latest",
    "lint-staged": "latest",
    "npm-run-all": "latest",
    "chokidar-cli": "latest",
    "onchange": "latest"
  },
  
  "componentLibraries": {
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "latest"
  },
  
  "docker": {
    "node": "22-stable",
    "nginx": "alpine"
  }
}
```

**Version Strategies**:
- `"latest"` - Fetch current version from npm registry (e.g., `npm view vue version`)
- `"^6.3.5"` - Pin to specific version with caret (allows minor/patch updates)
- `"~6.3.5"` - Pin with tilde (allows patch updates only)
- `"6.3.5"` - Exact version (no updates)

**Note**: This configuration is embedded in this SKILL.md file. There is no separate versions.json file needed.

## References

- **Version Configuration**: See "Version Configuration (versions.json)" section above in this file
- **Package Templates**: `reference/` directory (jest-vuex.md, vitest-pinia.md, latest-versions.md)
- **Complete Examples**: `examples.md`


