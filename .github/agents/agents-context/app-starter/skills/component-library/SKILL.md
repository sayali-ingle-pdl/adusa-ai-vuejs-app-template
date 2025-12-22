# Component Library Installation Skill

## Purpose
Conditionally install and configure the component library based on user request and home directory GitHub token availability.

## ⚠️ CONDITIONAL SKILL - READ CAREFULLY

**Execute this skill ONLY if**: `include_component_library: yes`

**If `include_component_library: no`**: 
- **SKIP this skill entirely**
- Do not validate token
- Do not add to package.json
- Move to next skill

## 🚨 MANDATORY REQUIREMENTS

**Prerequisites for component library installation**:
1. User answered `yes` to component library question
2. GitHub authentication token exists in `~/.npmrc` (home directory)

**Do not ask user for token:** Never ask user to provide token.

**If token missing**: Inform user with setup instructions, offer to skip installation

## When to Use
Execute this skill during package.json generation (after user confirms component library installation).

## Input Parameters
From configuration:
- `include_component_library` - Boolean flag (`yes` or `no`)
- `component_library` - Name of the component library package (default: `@RoyalAholdDelhaize/pdl-spectrum-component-library-web`)
- `component_library_version` - Version of the component library (fetch latest if not specified)

## Package.json Format

**Important**: The component library uses an npm alias format in package.json:

```json
{
  "dependencies": {
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^{version}"
  }
}
```

**Why the alias?**
- **Key (lowercase)**: `@royalaholddelhaize/pdl-spectrum-component-library-web` - npm convention for lowercase scopes
- **Value (original case)**: `npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^{version}` - actual package name

**Manual installation command**:
```bash
npm install @RoyalAholdDelhaize/pdl-spectrum-component-library-web
```
npm automatically creates the alias format in package.json.

## 🔍 BEFORE GENERATING - CRITICAL CHECKS

Perform these checks in order:

1. **Conditional Check**: Verify `include_component_library: yes`
   - If `no` → Exit this skill immediately
   - If `yes` → Continue to step 2

2. **Home Directory Token Validation**: Check if `~/.npmrc` exists
   - **Safe check**: `test -f ~/.npmrc && echo "exists" || echo "missing"`
   - **DO NOT read token contents** (security risk)
   - If exists → Assume token configured, continue
   - If missing → Show setup instructions, offer to skip

3. **User Decision if Token Missing**:
   - Display: "⚠️ GitHub token not found in ~/.npmrc"
   - Display: "📖 Setup instructions: https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-with-a-personal-access-token"
   - Ask: "Would you like to skip component library installation? (yes/no)"
   - If yes → Set `include_component_library: no`, skip installation
   - If no → Assume user will configure token, continue with warning

## Key Rule
**Component library is ONLY installed when**:
- `include_component_library: yes` AND
- `~/.npmrc` file exists (or user chooses to continue despite warning)

## Implementation Steps

### Step 1: Conditional Check

```javascript
// ONLY execute if user requested component library
if (userConfig.include_component_library !== 'yes') {
  console.log('⏭️  Skipping component library installation - not requested');
  return; // Exit this skill
}
```

### Step 2: Validate Home Directory Token

```javascript
const fs = require('fs');
const path = require('path');
const os = require('os');

// Check if ~/.npmrc exists (DO NOT read contents for security)
const homeNpmrc = path.join(os.homedir(), '.npmrc');
const tokenExists = fs.existsSync(homeNpmrc);

if (!tokenExists) {
  console.log('⚠️  GitHub token not found in ~/.npmrc');
  console.log('📖 Setup instructions: https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-with-a-personal-access-token');
  
  // Prompt user to skip or continue
  const skipInstallation = prompt('Would you like to skip component library installation? (yes/no): ');
  
  if (skipInstallation.toLowerCase() === 'yes') {
    console.log('✓ Skipping component library installation');
    userConfig.include_component_library = 'no';
    return; // Exit this skill
  } else {
    console.log('⚠️  Continuing with installation. Please configure token before running npm install.');
  }
} else {
  console.log('✓ GitHub authentication configured in ~/.npmrc');
}
```

### Step 3: Add Component Library to package.json

```javascript
// In package.json generation
if (userConfig.include_component_library === 'yes') {
  // Fetch latest version if not specified
  if (!userConfig.component_library_version) {
    const { execSync } = require('child_process');
    try {
      const latestVersion = execSync(
        'npm view @RoyalAholdDelhaize/pdl-spectrum-component-library-web version --registry=https://npm.pkg.github.com',
        { encoding: 'utf-8' }
      ).trim();
      userConfig.component_library_version = `^${latestVersion}`;
    } catch (error) {
      console.log('⚠️  Could not fetch latest version, using default');
      userConfig.component_library_version = '^1.0.0';
    }
  }
  
  // Add to dependencies using npm alias format
  // Key: lowercase scope (npm convention)
  // Value: npm:{original case package}@{version}
  const aliasKey = '@royalaholddelhaize/pdl-spectrum-component-library-web';
  const aliasValue = `npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@${userConfig.component_library_version}`;
  
  dependencies[aliasKey] = aliasValue;
  
  console.log(`✓ Component library configured: ${aliasKey} → ${aliasValue}`);
}
```

### Step 4: Trigger npmrc Skill

```javascript
// The npmrc skill will handle .npmrc generation
// Project .npmrc will contain ONLY registry mapping (no token)
// Token remains in ~/.npmrc (home directory)

if (userConfig.include_component_library === 'yes') {
  // Trigger npmrc skill to generate project .npmrc
  console.log('✓ Project .npmrc will be generated by npmrc skill');
  console.log('ℹ️  Authentication uses token from ~/.npmrc\n');
}
```

## Decision Tree

### Case 1: Component Library Requested + Token Exists ✅
```javascript
{ include_component_library: "yes" }
// AND ~/.npmrc file exists
```
**Action**: Install component library, generate project .npmrc (registry only)

### Case 2: Component Library Requested + Token Missing ⚠️
```javascript
{ include_component_library: "yes" }
// BUT ~/.npmrc file missing
```
**Action**: Show warning with setup docs, prompt user to skip or continue

### Case 3: Component Library Not Requested ✅
```javascript
{ include_component_library: "no" }
```
**Action**: Skip skill entirely, no validation needed

## Output
Component library added to `package.json` dependencies using npm alias format:
```json
{
  "dependencies": {
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^{latest_version}"
  }
}
```
- **Store token in `~/.npmrc` ONLY** (home directory, never in project)
- **Project `.npmrc`** contains registry mapping only (safe to commit)
- **Never commit tokens** to version control

### Token Configuration
```bash
# ~/.npmrc (home directory - secure)
//npm.pkg.github.com/:_authToken=ghp_your_token_here

# Project .npmrc (safe to commit)
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```

### Token Permissions
- Minimum required scope: `read:packages`
- Set expiration date for tokens
- Use fine-grained tokens when possible
- Create at: https://github.com/settings/tokens

### File Permissions
```bash
# Ensure ~/.npmrc is not world-readable
chmod 600 ~/.npmrc
```

## Postconditions
When this skill completes successfully, the component library is added to `package.json` dependencies.

## Integration with Other Skills

### Affects package-json Skill
The `include_component_library` flag is used by the package-json skill to determine whether to include the component library in dependencies.

### Affects npmrc Skill
The `include_component_library` flag triggers npmrc skill to generate project `.npmrc` with registry configuration (no embedded token).

## Related Skills
- **configuration**: Gathers `include_component_library` flag from user prompts
- **package-json**: Uses `include_component_library` flag to add dependencies
- **npmrc**: Creates project `.npmrc` with registry mapping (token in `~/.npmrc`)

## Validation Checklist

- [ ] Verify `include_component_library` flag checked before execution
- [ ] Verify `~/.npmrc` existence validated (not contents)
- [ ] Verify user informed if token missing
- [ ] Verify component library added to package.json when conditions met
- [ ] Verify npmrc skill triggered to generate project `.npmrc`
- [ ] Verify no token embedded in project files
