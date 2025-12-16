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

### SKILL.md (Required) - AI-Consumable Template
The main instruction file that defines:
- **Purpose**: What the skill does
- **Instructions**: Step-by-step process to execute
- **Input Parameters**: What data is needed
- **Output**: What files/artifacts are created
- **Validation**: How to verify the output

**Target size**: < 100 lines
**Audience**: GitHub Copilot agents
**Style**: Concise, template-focused, minimal prose

This file is always loaded when the skill is invoked.

### reference.md (Optional) - AI-Consumable Reference
Detailed reference documentation including:
- Complete API specifications
- Configuration options
- Dependency lists with version numbers
- Technical details

**Target size**: < 200 lines
**Audience**: GitHub Copilot agents
**Style**: Factual, structured data

Load this file when you need detailed specs.

### examples.md (Optional) - AI-Optimized Examples
Real-world examples showing:
- Common use cases
- Different configurations
- Complete file samples
- Template variable substitutions

**Target size**: < 300 lines
**Audience**: GitHub Copilot agents
**Style**: Code-first, minimal explanations, critical notes only

Load this file when you need to see concrete examples.

### *_GUIDE.md (Optional) - Human Documentation
Comprehensive tutorials and explanations:
- Detailed explanations of how things work
- Why certain patterns are used
- Troubleshooting guides
- Best practices
- Step-by-step tutorials

**Target size**: Unlimited
**Audience**: Developers
**Style**: Educational, comprehensive, tutorial-focused

Created for developer reference, not consumed by AI agents.

### scripts/ (Optional)
Utility scripts for:
- Validation
- Code generation
- Analysis
- Automation

Execute these scripts, don't load them as context.

## Documentation Philosophy

### Token Efficiency for AI Agents
AI agents consume tokens when reading documentation. To optimize:

1. **SKILL.md**: Keep under 100 lines - template + essential notes
2. **examples.md**: Keep under 300 lines - show code, minimize prose
3. **reference.md**: Keep under 200 lines - version specs + compatibility
4. **Move verbose content to *_GUIDE.md**: Detailed explanations for developers

### AI vs Human Documentation
- **AI needs**: What to generate (code templates, patterns)
- **Humans need**: Why and how it works (explanations, tutorials)

**If it explains HOW or WHY** → Put in *_GUIDE.md
**If it shows WHAT to generate** → Keep in examples.md

### Optimization Status

#### Optimized Skills
- ✅ `package-json/`: Split into examples.md (184 lines) + PACKAGE_GUIDE.md

#### Skills Needing Optimization
Large examples.md files (>500 lines) that may benefit from splitting:
- `nginx-default-conf/` (818 lines)
- `jest-config/` (739 lines)
- `stylelintrc/` (680 lines)
- `sonar-properties/` (672 lines)
- `nginx-sites-available/` (580 lines)
- `prettier-config/` (564 lines)
- `eslint-config/` (558 lines)
- `prettierignore/` (554 lines)

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
- `testing/` - Test file generation patterns and best practices
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
