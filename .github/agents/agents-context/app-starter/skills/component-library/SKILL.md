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

```
┌─────────────────────────────────────┐
│ Check github_token                  │
└──────────┬──────────────────────────┘
           │
           ├─ No Token / Empty ────────────────┐
           │                                   │
           │                                   ▼
           │                          ┌────────────────────┐
           │                          │ Skip Component Lib │
           │                          │ - Clear lib name   │
           │                          │ - Clear lib version│
           │                          │ - Show warning     │
           │                          └────────────────────┘
           │
           └─ Token Provided ──────────────────┐
                                               │
                                               ▼
                                      ┌─────────────────────┐
                                      │ Check component_lib │
                                      └──────┬──────────────┘
                                             │
                                             ├─ Not Set / Empty ──────┐
                                             │                        │
                                             │                        ▼
                                             │               ┌─────────────────┐
                                             │               │ Skip Comp Lib   │
                                             │               │ (No lib needed) │
                                             │               └─────────────────┘
                                             │
                                             └─ Library Set ──────────┐
                                                                      │
                                                                      ▼
                                                             ┌──────────────────┐
                                                             │ Install Comp Lib │
                                                             │ - Add to deps    │
                                                             │ - Create .npmrc  │
                                                             │ - Add token      │
                                                             └──────────────────┘
```

## Instructions

### Step 1: Validate Component Library Requirements

```javascript
// Component library requires BOTH library name AND github token
// If github_token is missing, skip component library installation

if (!userConfig.github_token || userConfig.github_token.trim() === '') {
  if (userConfig.component_library && userConfig.component_library !== '') {
    console.log('⚠️  GitHub token not provided. Skipping component library installation.');
    console.log('   To include component library, provide github_token in config.json or when prompted.\n');
  }
  
  // Clear component library settings when no token
  userConfig.component_library = '';
  userConfig.component_library_version = '';
}

// Set flag for package.json generation
userConfig.include_component_library = Boolean(
  userConfig.component_library && 
  userConfig.component_library.trim() !== '' &&
  userConfig.github_token &&
  userConfig.github_token.trim() !== ''
);
```

### Step 2: Add Component Library to package.json (Conditional)

```javascript
// In package.json generation
if (userConfig.include_component_library) {
  // Add to dependencies
  dependencies[userConfig.component_library] = userConfig.component_library_version;
  
  console.log(`✓ Component library configured: ${userConfig.component_library}@${userConfig.component_library_version}`);
}
```

### Step 3: Generate .npmrc (Conditional)

```javascript
// Only create .npmrc if component library is included
if (userConfig.include_component_library) {
  const npmrcContent = `@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${userConfig.github_token}
`;
  
  fs.writeFileSync('.npmrc', npmrcContent.trim());
  console.log('✓ .npmrc created with GitHub authentication\n');
}
```

### Step 4: Add .npmrc to .gitignore (Always)

```javascript
// Ensure .npmrc is in .gitignore to protect token
const gitignoreContent = fs.readFileSync('.gitignore', 'utf-8');

if (!gitignoreContent.includes('.npmrc')) {
  fs.appendFileSync('.gitignore', '\n.npmrc\n');
  console.log('✓ Added .npmrc to .gitignore for security');
}
```

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

## Use Cases

### Case 1: Both Provided ✅
```json
{
  "component_library": "@myorg/components",
  "component_library_version": "^2.0.0",
  "github_token": "ghp_abc123..."
}
```
**Result**: Component library installed, .npmrc created with token

### Case 2: Library but No Token ⚠️
```json
{
  "component_library": "@myorg/components",
  "component_library_version": "^2.0.0",
  "github_token": ""
}
```
**Result**: Component library skipped, warning shown

### Case 3: Token but No Library ✅
```json
{
  "component_library": "",
  "github_token": "ghp_abc123..."
}
```
**Result**: No component library (as expected), token ignored

### Case 4: Neither Provided ✅
```json
{
  "component_library": "",
  "github_token": ""
}
```
**Result**: No component library (as expected)

### Case 5: Using Defaults (No config.json)
**Default Values**:
- `component_library`: `"@royalaholddelhaize/pdl-spectrum-component-library-web"`
- `github_token`: Not set

**Result**: User will be prompted for github_token. If they skip, component library is not installed.

## User Messages

### Warning When Token Missing
```
⚠️  GitHub token not provided. Skipping component library installation.
   To include component library, provide github_token in config.json or when prompted.
```

### Success When Both Provided
```
✓ Component library configured: @myorg/components@^2.0.0
✓ .npmrc created with GitHub authentication
```

## Security Considerations

- ✅ GitHub token is never logged or displayed in clear text
- ✅ `.npmrc` is always in `.gitignore`
- ✅ Warning shown when library specified but token missing
- ✅ Token is only used when both library and token are present
- ✅ Empty string tokens are treated as "not provided"
- ✅ Token validation happens before any file creation

## Files Generated

### .npmrc (when component library included)
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_YOUR_TOKEN_HERE
```

### package.json (when component library included)
```json
{
  "dependencies": {
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "^1.0.3"
  }
}
```

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
