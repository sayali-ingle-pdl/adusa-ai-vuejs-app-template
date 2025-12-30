# GitHub Copilot Agents

This directory contains all GitHub Copilot agents for the Vue 3 Vite application template project.

## Directory Structure

```
.github/agents/
├── app-starter.agent.md      # @app-starter agent definition
├── app-starter/              # App-starter context directory
│   └── agents-context/       
│       ├── .copilot-instructions.md  # Complete execution instructions
│       └── skills/           # Individual file generation skills
│           ├── package-json/
│           ├── vite-config/
│           ├── tsconfig/
│           └── ...           # 40+ skills
│
└── shared/                   # Shared resources across agents
    ├── README.md
    └── templates/            # Common templates
```

## Current Agents

### @app-starter
**Purpose**: Bootstrap complete Vue 3 Vite applications from scratch

**Location**: `app-starter.agent.md` (context in `app-starter/agents-context/`)

**Usage**: `@app-starter generate a new Vue 3 application`

**Capabilities**:
- Interactive configuration via questionnaire
- Generates 40+ configuration and source files
- Sets up build tooling (Vite, TypeScript, ESLint, etc.)
- Creates application structure (views, router, store, etc.)
- Configures testing framework (Vitest or Jest)
- Sets up Docker and deployment configs
- Validates and verifies the generated application

**Documentation**:
- Agent overview: `app-starter.agent.md`
- Detailed instructions: `app-starter/agents-context/.copilot-instructions.md`
- Skills documentation: `app-starter/agents-context/skills/*/SKILL.md`

## Adding New Agents

When adding new agents, follow this scalable pattern:

### 1. Create Agent Directory

```bash
mkdir -p .github/agents/{agent-name}/agents-context/skills
```

### 2. Create Agent Definition

Create `{agent-name}.agent.md` in `.github/agents/`:

```markdown
---
name: agent-name
description: Brief description of what this agent does
---

# Agent Name

## Detailed Instructions

See `./{agent-name}/agents-context/.copilot-instructions.md` for complete workflow.

## Skills

List of skills this agent uses...
```

### 3. Create Detailed Instructions

Create `{agent-name}/agents-context/.copilot-instructions.md`:

```markdown
# Agent Name - Detailed Instructions

## User Input
What questions to ask users...

## Execution Flow
Step-by-step execution logic...

## Skills
How to orchestrate skills...
```

### 4. Add Skills

Create skill directories under `{agent-name}/agents-context/skills/{skill-name}/`:
- `SKILL.md` - Skill instructions
- `examples.md` - Example outputs (optional)
- `templates/` - Template files (optional)

### 5. Update Root Documentation

Update `.github/copilot-instructions.md` to reference the new agent:

```markdown
### @agent-name
Brief description...

**Usage**: `@agent-name <command>`

**Detailed Instructions**: See `agents/{agent-name}/agents-context/.copilot-instructions.md`
```

## Agent Collaboration

Agents can reference other agents or shared resources:

### Calling Other Agents
```markdown
After generating the component, invoke @test-generator to create tests.
```

### Using Shared Resources
```markdown
For Vue component templates, see `../shared/templates/vue-components/`
```

## Best Practices

### Agent Design
- ✅ Single Responsibility: Each agent should have a clear, focused purpose
- ✅ Modular Skills: Break down complex tasks into discrete skills
- ✅ Clear Documentation: Provide detailed instructions and examples
- ✅ Validation: Include verification steps to ensure correctness

### Shared Resources
- ✅ Extract common patterns to `shared/` when used by 2+ agents
- ✅ Keep agent-specific logic in agent directories
- ✅ Document shared resource usage in agent instructions
- ✅ Version shared templates if breaking changes are possible

### File Organization
- ✅ Keep agent.md concise (overview only)
- ✅ Put detailed logic in .copilot-instructions.md
- ✅ Organize skills by functional category
- ✅ Use consistent naming conventions

### Testing Agents
Before committing new agents:
- ✅ Test with various user inputs
- ✅ Verify all file paths are correct
- ✅ Ensure generated code follows conventions
- ✅ Check that validation steps work

## Future Agents (Suggested)

### @feature-builder
Generate features/components in existing applications

**Structure**:
```
.github/agents/
├── feature-builder.agent.md
└── feature-builder/
    └── agents-context/
```
- Component generator
- View generator
- Service layer generator
- Store module generator

### @code-reviewer
Review and improve existing code
- Best practices checker
- Performance analyzer
- Refactoring suggestions
- Code quality reports

### @test-generator
Generate comprehensive tests
- Unit test generator
- Integration test generator
- E2E test scaffolding
- Test coverage analyzer

### @deployment-helper
Deployment and CI/CD assistance
- CI/CD pipeline generator
- Environment configuration
- Docker optimization
- Deployment checklists

## Maintenance

### When to Update Agents
- New Vue/Vite versions with breaking changes
- New best practices or patterns emerge
- User feedback on generated code quality
- Bug fixes or edge cases discovered

### Version Management
Consider versioning agents if:
- Breaking changes to skill interfaces
- Different template versions for different Vue versions
- Need to support multiple project types simultaneously

### Documentation Updates
Keep documentation in sync:
- Update agent.md when capabilities change
- Update .copilot-instructions.md when workflow changes
- Update skill SKILL.md files when templates change
- Update this README when adding/removing agents
