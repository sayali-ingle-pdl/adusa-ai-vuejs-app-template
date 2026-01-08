---
name: component-library
description: Conditionally installs GitHub Packages component library at latest version. Adds to package.json when user requests it.
---

# Component Library Installation Skill

## Purpose
Install and configure the latest version of the component library when user requests it.

## ⚠️ CONDITIONAL SKILL - READ CAREFULLY

**Execute this skill ONLY if**: `include_component_library: yes`

**If `include_component_library: no`**: 
- **SKIP this skill entirely**
- Move to next skill

## Input Parameters
From configuration:
- `include_component_library` - Boolean flag (`yes` or `no`)
- `component_library` - Name of the component library package (default: `@RoyalAholdDelhaize/pdl-spectrum-component-library-web`)
- `component_library_version` - Always fetch latest version from npm registry

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

## Key Rule
**Component library is installed when**: `include_component_library: yes`

**If fetching latest version fails**:
- Skip installation
- Inform user that GitHub token is missing
- Provide setup instructions
- Continue with project generation

## Implementation Steps

### Step 1: Conditional Check

```javascript
// ONLY execute if user requested component library
if (userConfig.include_component_library !== 'yes') {
  console.log('⏭️  Skipping component library installation - not requested');
  return; // Exit this skill
}
```

### Step 2: Fetch Latest Version and Add to package.json

```javascript
// In package.json generation
if (userConfig.include_component_library === 'yes') {
  const { execSync } = require('child_process');
  
  try {
    // Fetch latest version from npm registry
    // Note: Uses npm show without --registry flag to respect ~/.npmrc authentication
    const latestVersion = execSync(
      'npm show @RoyalAholdDelhaize/pdl-spectrum-component-library-web version',
      { encoding: 'utf-8' }
    ).trim();
    
    userConfig.component_library_version = `^${latestVersion}`;
    
    // Add to dependencies using npm alias format
    // Key: lowercase scope (npm convention)
    // Value: npm:{original case package}@{version}
    const aliasKey = '@royalaholddelhaize/pdl-spectrum-component-library-web';
    const aliasValue = `npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@${userConfig.component_library_version}`;
    
    dependencies[aliasKey] = aliasValue;
    
    console.log(`✓ Component library configured: ${aliasKey} → ${aliasValue}`);
    
  } catch (error) {
    // If fetching version fails, inform user and skip installation
    console.log('\n⚠️  GitHub token is missing. Skipping component library installation.');
    console.log('📖 To install the component library later, configure a GitHub token:');
    console.log('   https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-with-a-personal-access-token\n');
    
    // Skip installation - do not add to dependencies
    userConfig.include_component_library = 'no';
    return;
  }
}
```

## Validation Checklist

- [ ] Verify `include_component_library` flag checked before execution
- [ ] Verify latest version fetched successfully before adding to package.json
- [ ] Verify user informed with setup instructions if fetch fails
- [ ] Verify component library added to package.json only when fetch succeeds
