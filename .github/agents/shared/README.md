# Shared Agent Resources

This directory contains resources shared across multiple agents to avoid duplication and ensure consistency.

## Purpose

When multiple agents need the same templates, utilities, or configuration patterns, they should be centralized here rather than duplicated in each agent's context directory.

## Structure

```
shared/
├── templates/           # Reusable code templates
│   ├── vue-components/  # Vue component templates (composition/options API)
│   ├── test-templates/  # Test file templates (vitest/jest)
│   └── service-layer/   # API service templates
└── utilities/           # Common utilities and patterns
    ├── template-engine/ # Template processing logic
    └── validation/      # Shared validation rules
```

## Usage Guidelines

### When to Add Shared Resources

Add resources to `shared/` when:
- ✅ Multiple agents will use the same template/pattern
- ✅ Consistency across agents is critical
- ✅ Changes should propagate to all agents
- ✅ The resource is stable and well-tested

### When NOT to Use Shared

Keep resources agent-specific when:
- ❌ Only one agent uses it
- ❌ The resource is experimental/unstable
- ❌ Agent-specific customization is needed
- ❌ The pattern is tightly coupled to one agent's workflow

## Agent Reference Pattern

Agents should reference shared resources using relative paths:

```markdown
For Vue component templates, see:
`../../shared/templates/vue-components/`

For test templates, see:
`../../shared/templates/test-templates/`
```

## Future Agents

When adding new agents, follow this pattern:

```
.github/agents/
├── app-starter/
│   ├── agent.md
│   └── agents-context/
│       ├── .copilot-instructions.md
│       └── skills/
├── feature-builder/        # Future agent
│   ├── agent.md
│   └── agents-context/
│       ├── .copilot-instructions.md
│       └── skills/
└── shared/                 # Shared resources
    └── templates/
```

## Current Status

**Status**: Empty - Ready for future shared resources

When adding your next agent, identify common patterns from existing agents and migrate them here.
