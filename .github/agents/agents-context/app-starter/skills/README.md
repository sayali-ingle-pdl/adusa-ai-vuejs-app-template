# Skills Directory Structure

This directory contains all skills for the App Starter Agent. Each skill is organized in its own directory following a standardized structure.

## Directory Structure

```
skills/
├── package-json/              # Example skill directory
│   ├── SKILL.md              # Main instructions (always loaded)
│   ├── reference.md          # API/configuration reference (loaded as needed)
│   ├── examples.md           # Usage examples (loaded as needed)
│   └── scripts/              # Optional utility scripts
│       └── validate.py       # Example validation script
├── vite-config/
│   ├── SKILL.md
│   ├── reference.md
│   └── examples.md
└── [other-skills]/
    ├── SKILL.md
    └── ...
```

## File Conventions

### SKILL.md (Required)
The main instruction file that defines:
- **Purpose**: What the skill does
- **Instructions**: Step-by-step process to execute
- **Input Parameters**: What data is needed
- **Output**: What files/artifacts are created
- **Validation**: How to verify the output

This file is always loaded when the skill is invoked.

### reference.md (Optional)
Detailed reference documentation including:
- Complete API specifications
- Configuration options
- Dependency lists
- Technical details

Load this file when you need detailed information.

### examples.md (Optional)
Real-world examples showing:
- Common use cases
- Different configurations
- Complete file samples
- Edge cases

Load this file when you need to see concrete examples.

### scripts/ (Optional)
Utility scripts for:
- Validation
- Code generation
- Analysis
- Automation

Execute these scripts, don't load them as context.

## Usage Pattern

When executing a skill:

1. **Always load** `SKILL.md` first
2. **Load `reference.md`** if you need detailed specs
3. **Load `examples.md`** if you need to see samples
4. **Execute scripts** if validation or automation is needed

## Available Skills

### Configuration and Orchestration
- `configuration/` - Read and merge config.json with interactive prompts
- `conditional-generation/` - Determine files to generate based on app type
- `component-library/` - Conditionally install component library with GitHub token

### Package Management
- `package-json/` - Generate package.json with dependencies
- `npmrc/` - Generate .npmrc configuration

### Build and Development
- `vite-config/` - Generate Vite configuration
- `tsconfig/` - Generate TypeScript configuration
- `env-files/` - Generate environment files

### Code Quality
- `eslint-config/` - Generate ESLint configuration
- `prettier-config/` - Generate Prettier configuration
- `gitignore/` - Generate .gitignore file
- `editorconfig/` - Generate .editorconfig

### Testing
- `jest-config/` - Generate Jest configuration
- `babel-config/` - Generate Babel configuration

### Application Structure
- `main-entry/` - Generate main.ts entry point
- `app-component/` - Generate App.vue component
- `router/` - Generate Vue Router setup
- `store/` - Generate Vuex store
- `env-constants/` - Generate environment constants
- `global-constants/` - Generate global constants
- `typescript-shims/` - Generate TypeScript declarations
- `theme/` - Generate SCSS theme files
- `view-components/` - Generate view components

### Docker and Deployment
- `docker/` - Generate Dockerfile and nginx config
- `husky/` - Generate Git hooks

### HTML
- `index-html/` - Generate index.html entry point

## Creating a New Skill

1. Create a new directory with kebab-case name
2. Add `SKILL.md` with the required structure
3. Optionally add `reference.md` and `examples.md`
4. Update this README with the new skill
5. Update `app-starter.agent.md` to list the new skill

## Example: Package JSON Skill

```
package-json/
├── SKILL.md          # Instructions for generating package.json
├── reference.md      # Complete dependency lists and script details
└── examples.md       # Sample package.json files for different scenarios
```

When invoked:
- Agent loads `SKILL.md` to understand the task
- Agent loads `reference.md` for complete dependency lists
- Agent loads `examples.md` to see patterns
- Agent generates `package.json` at project root
