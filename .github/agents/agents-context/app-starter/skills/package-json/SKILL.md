# Package JSON Skill

## Purpose
Generate the `package.json` file for a Vue 3 Vite application with all necessary dependencies, dev dependencies, and scripts.

## Instructions

1. **Gather Input Parameters** from user (see Input Parameters section below)
2. **Load Reference** file `reference.md` for complete dependency list
3. **Generate** package.json using the template structure
4. **Validate** output meets all requirements
5. **Create** the file at project root: `package.json`

## Input Parameters

Ask the user or read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_name`: The application name in kebab-case
- `project_scope`: The NPM scope/organization
- `default_port`: The development server port
- `vue_version`: Vue version to use (default: `^3.5.13`)
- `vite_version`: Vite version to use (default: `^6.3.5`)
- `typescript_version`: TypeScript version to use (default: `4.9`)

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
