# Package JSON Skill

## Purpose
Generate the `package.json` file for a Vue 3 Vite application with all necessary dependencies, dev dependencies, and scripts using the **latest stable versions**.

## Instructions

**CRITICAL**: You MUST fetch the latest version for EVERY package. Never assume or hardcode versions.

### Step 1: Gather Input Parameters
Ask the user or read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_name`: The application name in kebab-case
- `project_scope`: The NPM scope/organization
- `default_port`: The development server port
- `application_type`: "micro-frontend" or "standalone"
- `test_framework`: "jest" or "vitest"
- `state_management`: "vuex" or "pinia"

### Step 2: Fetch Latest Versions for ALL Packages

**MANDATORY**: Run `npm view <package> version` for EVERY SINGLE package before adding it to package.json.

**IMPORTANT**: For testing framework compatibility:
- If using Jest: Check `@vue/vue3-jest` version first - this determines the maximum Jest version you can use
- All Jest-related packages (`jest`, `babel-jest`, `ts-jest`, `@types/jest`, `jest-environment-jsdom`) MUST match the major version
- If `@vue/vue3-jest` is stuck at v29, then use Jest 29 for all Jest packages
- If using Vitest: All packages can use latest versions

### Step 3: Load Reference File
Read `reference.md` for the complete package list and script templates

### Step 4: Generate package.json
Use the fetched versions to create the package.json file

### Step 5: Create the File
Save to project root: `package.json`

## Input Parameters

Ask the user or read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_name`: The application name in kebab-case
- `project_scope`: The NPM scope/organization
- `default_port`: The development server port
- `vue_version`: Vue version to use
- `vite_version`: Vite version to use
- `typescript_version`: TypeScript version to use

## Output
- File: `package.json` at project root
- Format: Valid JSON with proper indentation (2 spaces)


## Validation Checklist

After creating package.json and running `npm install`:

### 1. Version Validation
```bash
npm outdated
```
**Expected Result**: No outdated packages (all should show "Current" = "Wanted" = "Latest")

**If outdated packages are found:**
- ❌ STOP - You made a mistake in version fetching
- ✅ For Jest packages: Check if they need to match @vue/vue3-jest version
- ✅ For other packages: Update to latest unless there's a compatibility constraint
- ✅ Document any version constraints in README.md

### 2. Security Validation
```bash
npm audit
```
**Expected Result**: 0 vulnerabilities

### 3. Compatibility Validation
- ✅ All Jest packages have matching major versions
- ✅ TypeScript version is compatible with @typescript-eslint packages
- ✅ ESLint version is compatible with all eslint-plugin-* packages
- ✅ Sass version is compatible with sass-loader

### 4. Structure Validation
- ✅ Package name follows format: `@scope/name` or just `name`
- ✅ `type` field is set to `"module"`
- ✅ All required scripts are defined
- ✅ Valid JSON syntax (no trailing commas, proper quotes)

## Common Pitfalls to Avoid

### ❌ DON'T:
- Hardcode versions based on examples
- Skip version checking for "minor" packages like @types/*
- Assume all packages can use latest versions
- Forget to check Jest ecosystem compatibility
- Copy versions from reference.md examples

### ✅ DO:
- Run `npm view` for EVERY package
- Check compatibility constraints (especially Jest with @vue/vue3-jest)
- Use caret (^) for version ranges
- Verify after installation with `npm outdated`
- Document any intentional version pinning