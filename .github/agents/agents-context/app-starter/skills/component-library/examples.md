# Component Library Installation Examples

## Example 1: Full Installation (Both Library and Token)

### Configuration
```json
{
  "component_library": "@royalaholddelhaize/pdl-spectrum-component-library-web",
  "component_library_version": "^1.0.3",
  "github_token": "ghp_abc123def456ghi789"
}
```

### Output

**Console Output:**
```
✓ Component library configured: @royalaholddelhaize/pdl-spectrum-component-library-web@^1.0.3
✓ .npmrc created with GitHub authentication
✓ Added .npmrc to .gitignore for security
```

**package.json:**
```json
{
  "name": "@myorg/my-app-web",
  "version": "0.1.0",
  "dependencies": {
    "vue": "^3.5.13",
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "^1.0.3",
    "axios": "^1.7.9"
  }
}
```

**.npmrc:**
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_abc123def456ghi789
```

**.gitignore:**
```
node_modules/
dist/
.npmrc
```

---

## Example 2: Library Specified but Token Missing

### Configuration
```json
{
  "component_library": "@royalaholddelhaize/pdl-spectrum-component-library-web",
  "component_library_version": "^1.0.3",
  "github_token": ""
}
```

### Output

**Console Output:**
```
⚠️  GitHub token not provided. Skipping component library installation.
   To include component library, provide github_token in config.json or when prompted.
```

**package.json:**
```json
{
  "name": "@myorg/my-app-web",
  "version": "0.1.0",
  "dependencies": {
    "vue": "^3.5.13",
    "axios": "^1.7.9"
  }
}
```

**Files NOT Created:**
- `.npmrc` (not created)

**Internal State:**
```javascript
userConfig.component_library = '';  // Cleared
userConfig.component_library_version = '';  // Cleared
userConfig.include_component_library = false;
```

---

## Example 3: Token Provided but No Library

### Configuration
```json
{
  "component_library": "",
  "github_token": "ghp_abc123def456ghi789"
}
```

### Output

**Console Output:**
```
(No output - silent as expected)
```

**package.json:**
```json
{
  "name": "@myorg/my-app-web",
  "version": "0.1.0",
  "dependencies": {
    "vue": "^3.5.13",
    "axios": "^1.7.9"
  }
}
```

**Files NOT Created:**
- `.npmrc` (not created, no library to install)

**Internal State:**
```javascript
userConfig.include_component_library = false;
```

---

## Example 4: Neither Library nor Token

### Configuration
```json
{
  "component_library": "",
  "github_token": ""
}
```

### Output

**Console Output:**
```
(No output - silent as expected)
```

**package.json:**
```json
{
  "name": "@myorg/my-app-web",
  "version": "0.1.0",
  "dependencies": {
    "vue": "^3.5.13",
    "axios": "^1.7.9"
  }
}
```

**Files NOT Created:**
- `.npmrc` (not created)

---

## Example 5: Interactive Prompt (Default Component Library)

### Initial Configuration
```json
{
  "component_library": "@royalaholddelhaize/pdl-spectrum-component-library-web"
}
```

### Agent Prompts User

**Console Output:**
```
📦 Component library configured: @royalaholddelhaize/pdl-spectrum-component-library-web
   GitHub token required for private package installation

? GitHub Personal Access Token (skip to exclude component library): 
```

### Scenario 5a: User Provides Token

**User Input:**
```
ghp_abc123def456ghi789
```

**Result:**
- ✓ Component library installed
- ✓ .npmrc created
- ✓ Success messages displayed

### Scenario 5b: User Skips Token

**User Input:**
```
[Enter with empty value]
```

**Result:**
- ⚠️ Warning displayed
- Component library NOT installed
- .npmrc NOT created

---

## Example 6: Custom Component Library

### Configuration
```json
{
  "component_library": "@custom/design-system",
  "component_library_version": "^2.5.0",
  "github_token": "ghp_custom_token_here"
}
```

### Output

**package.json:**
```json
{
  "dependencies": {
    "@custom/design-system": "^2.5.0"
  }
}
```

**.npmrc:**
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_custom_token_here
```

---

## Example 7: Multiple GitHub Packages

If you need multiple GitHub packages, the .npmrc supports global GitHub authentication:

### Configuration
```json
{
  "component_library": "@myorg/components",
  "component_library_version": "^1.0.0",
  "github_token": "ghp_your_token"
}
```

### Manual Addition to package.json
After generation, you can manually add more GitHub packages:

```json
{
  "dependencies": {
    "@myorg/components": "^1.0.0",
    "@myorg/utils": "^2.0.0",
    "@myorg/icons": "^1.5.0"
  }
}
```

All will use the same .npmrc authentication.

---

## Example 8: Validation Logic Flow

```javascript
// Example validation code
function validateComponentLibrary(config) {
  // Check 1: Token exists and not empty
  const hasToken = config.github_token && config.github_token.trim() !== '';
  
  // Check 2: Library exists and not empty
  const hasLibrary = config.component_library && config.component_library.trim() !== '';
  
  // Decision logic
  if (hasLibrary && !hasToken) {
    console.log('⚠️  GitHub token not provided. Skipping component library installation.');
    config.component_library = '';
    config.component_library_version = '';
    return false;
  }
  
  if (hasToken && hasLibrary) {
    console.log(`✓ Component library configured: ${config.component_library}@${config.component_library_version}`);
    return true;
  }
  
  // hasToken && !hasLibrary OR !hasToken && !hasLibrary
  return false;
}

// Set flag
config.include_component_library = validateComponentLibrary(config);
```

---

## Example 9: Error Handling

### Invalid Token Format

```javascript
// Validation in configuration skill should catch this
if (userConfig.github_token && !userConfig.github_token.startsWith('ghp_')) {
  console.warn('⚠️  Warning: GitHub token should start with "ghp_"');
  console.warn('   Token may be invalid. Installation may fail.\n');
}
```

### Network Error During Installation

After generation, when user runs `npm install`:

```bash
npm install
```

**If token is invalid:**
```
npm ERR! 401 Unauthorized - GET https://npm.pkg.github.com/@royalaholddelhaize/pdl-spectrum-component-library-web
npm ERR! Unable to authenticate, need: Basic
```

**Solution**: Update .npmrc with valid token

---

## Example 10: Security Best Practices

### DO ✅

1. **Use environment variable for token:**
```bash
# .env (local only, not committed)
GITHUB_TOKEN=ghp_your_token_here
```

2. **Generate token with minimal permissions:**
   - Only `read:packages` scope needed
   - Set expiration date
   - Use fine-grained tokens when possible

3. **Verify .gitignore includes .npmrc:**
```bash
# .gitignore
.npmrc
.env
.env.local
config.json
```

### DON'T ❌

1. **Don't commit .npmrc to git:**
```bash
git add .npmrc  # ❌ NEVER DO THIS
```

2. **Don't share tokens:**
   - Don't paste in Slack/email
   - Don't commit in config.json
   - Don't include in screenshots

3. **Don't use personal tokens in CI/CD:**
   - Use repository secrets
   - Use GitHub Actions secrets
   - Rotate regularly

---

## Testing Checklist

### Manual Testing

- [ ] Test with both library and token → Should install
- [ ] Test with library but no token → Should skip with warning
- [ ] Test with token but no library → Should skip silently
- [ ] Test with neither → Should skip silently
- [ ] Test interactive prompt with token provided → Should install
- [ ] Test interactive prompt with token skipped → Should skip
- [ ] Verify .npmrc in .gitignore → Should be present
- [ ] Verify token not displayed in console → Should be hidden
- [ ] Run `npm install` → Should successfully install library
- [ ] Verify custom library name works → Should support any @scope/name

### Automated Testing

```javascript
describe('Component Library Installation', () => {
  test('installs when both library and token provided', () => {
    const config = {
      component_library: '@myorg/components',
      component_library_version: '^1.0.0',
      github_token: 'ghp_test_token'
    };
    
    const result = validateComponentLibrary(config);
    expect(result).toBe(true);
    expect(config.include_component_library).toBe(true);
  });
  
  test('skips when token missing', () => {
    const config = {
      component_library: '@myorg/components',
      github_token: ''
    };
    
    const result = validateComponentLibrary(config);
    expect(result).toBe(false);
    expect(config.component_library).toBe('');
  });
});
```
