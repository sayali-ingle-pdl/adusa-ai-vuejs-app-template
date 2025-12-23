---
name: component-library
description: Conditionally installs GitHub Packages component library. Adds to package.json and triggers npmrc generation when requested.
---

# Component Library Installation Skill

## Purpose
Conditionally install and configure the component library based on the presence of both `component_library` and `github_token` configuration values.

## When to Use
Execute this skill during package.json generation and .npmrc creation (Step 7 in AGENT_INSTRUCTIONS.md).

## Input Parameters
From configuration:
- `component_library` - Name of the component library package
- `component_library_version` - Version of the component library
- `github_token` - GitHub Personal Access Token for private packages

## Key Rule
**Component library is ONLY installed when BOTH conditions are met:**
1. `component_library` is provided and not empty
2. `github_token` is provided and not empty

## Decision Tree
See: `examples.md` in this directory for decision tree diagram and code examples.

## Instructions
See: `examples.md` for detailed implementation steps and code examples.

## Output

### When Both Library and Token Provided ✅
- Component library added to `package.json` dependencies
- `.npmrc` file created with GitHub authentication
- Success messages displayed

### When Library Specified but Token Missing ⚠️
- Component library NOT added to `package.json`
- `.npmrc` NOT created
- Warning message displayed

### When Neither Provided ✅
- No component library (as expected)
- No `.npmrc` created
- Silent (no warnings)

## Security Considerations

- ✅ GitHub token is never logged or displayed in clear text
- ✅ `.npmrc` is always in `.gitignore`
- ✅ Warning shown when library specified but token missing
- ✅ Token is only used when both library and token are present
- ✅ Empty string tokens are treated as "not provided"
- ✅ Token validation happens before any file creation

## Integration with Other Skills

### Affects package-json Skill
The `include_component_library` flag is used by the package-json skill to determine whether to include the component library in dependencies.

### Affects npmrc Skill
The npmrc skill checks `include_component_library` to determine whether to create the .npmrc file.

## Related Skills
- **configuration**: Gathers component_library and github_token values
- **package-json**: Uses include_component_library flag to add dependencies
- **npmrc**: Creates .npmrc based on include_component_library flag

## Validation Checklist

- [ ] Verify component library added to package.json when both library and token provided
- [ ] Verify .npmrc created with correct token when both provided
- [ ] Verify warning shown when library set but token missing
- [ ] Verify no component library added when token missing
- [ ] Verify no .npmrc created when token missing
- [ ] Verify .npmrc in .gitignore
- [ ] Verify silent behavior when neither library nor token provided
- [ ] Verify token is not logged or displayed
