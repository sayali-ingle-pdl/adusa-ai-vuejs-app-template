# Component Library Conditional Installation - Summary

## Logic Flow

The agent will conditionally install the component library based on the presence of **both** `component_library` and `github_token`.

### Decision Tree

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

## Implementation Details

### Step 4.5 in AGENT_INSTRUCTIONS.md

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

## Files Updated

1. ✅ **AGENT_INSTRUCTIONS.md**
   - Added Step 4.5: Validate Component Library Requirements
   - Added Examples 5 & 6 showing token scenarios
   - Added `include_component_library` flag

2. ✅ **jest-vuex.md**
   - Added note about conditional installation

3. ✅ **jest-pinia.md**
   - Added note about conditional installation

4. ✅ **vitest-vuex.md**
   - Added note about conditional installation

5. ✅ **vitest-pinia.md**
   - Added note about conditional installation

## Agent Behavior

### When Generating package.json
```javascript
// In package.json generation
if (userConfig.include_component_library) {
  dependencies[userConfig.component_library] = 
    `npm:${userConfig.component_library.replace('@', '')}@${userConfig.component_library_version}`;
}
```

### When Generating .npmrc
```javascript
// Only create .npmrc if component library is included
if (userConfig.include_component_library) {
  const npmrcContent = `
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${userConfig.github_token}
`;
  fs.writeFileSync('.npmrc', npmrcContent);
}
```

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

- ✅ GitHub token is never logged or displayed
- ✅ .npmrc is always in .gitignore
- ✅ Warning shown when library specified but token missing
- ✅ Token is only used when both library and token are present
- ✅ Empty string tokens are treated as "not provided"

## Testing Scenarios

Test the agent with these configurations:

1. ✅ Full config with token → Should install library
2. ✅ Full config without token → Should skip library with warning
3. ✅ No library specified → Should skip (regardless of token)
4. ✅ Interactive mode, user skips token → Should skip library
5. ✅ Interactive mode, user provides token → Should install library
6. ✅ Partial config (library only) → Should prompt for token, skip if declined

## Summary

**Key Rule**: Component library is ONLY installed when BOTH conditions are met:
1. `component_library` is provided and not empty
2. `github_token` is provided and not empty

This ensures security (no token = no private package access) and prevents installation failures.
