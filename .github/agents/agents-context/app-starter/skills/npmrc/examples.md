# NPM RC Configuration Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains detailed explanations and extended examples.

## Basic Template

```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken={github_token}
```

## What Each Line Does

### 1. Scoped Registry Configuration
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```
- **Purpose**: Configures GitHub Packages as the registry for @RoyalAholdDelhaize scoped packages
- **Scope**: `@RoyalAholdDelhaize` - All packages under this organization
- **Registry**: `https://npm.pkg.github.com` - GitHub Packages npm registry
- **Impact**: npm will fetch @RoyalAholdDelhaize packages from GitHub instead of npmjs.org

### 2. Authentication Token
```
//npm.pkg.github.com/:_authToken=${GH_PACKAGE_LIBRARY_TOKEN}
```
- **Purpose**: Authenticates with GitHub Packages using a personal access token
- **Token Variable**: `${GH_PACKAGE_LIBRARY_TOKEN}` - Environment variable placeholder
- **Format**: npm configuration supports environment variable substitution
- **Security**: Keeps tokens out of committed files

## Setting Up Authentication

### Option 1: Environment Variable (Recommended)

#### On macOS/Linux
```bash
# Export token in current session
export GH_PACKAGE_LIBRARY_TOKEN=ghp_yourpersonalaccesstoken

# Or add to ~/.zshrc or ~/.bashrc for persistence
echo 'export GH_PACKAGE_LIBRARY_TOKEN=ghp_yourpersonalaccesstoken' >> ~/.zshrc
source ~/.zshrc

# Install packages
npm install
```

#### On Windows (PowerShell)
```powershell
# Set for current session
$env:GH_PACKAGE_LIBRARY_TOKEN="ghp_yourpersonalaccesstoken"

# Set permanently
[System.Environment]::SetEnvironmentVariable('GH_PACKAGE_LIBRARY_TOKEN', 'ghp_yourpersonalaccesstoken', 'User')

# Install packages
npm install
```

#### On Windows (Command Prompt)
```cmd
# Set for current session
set GH_PACKAGE_LIBRARY_TOKEN=ghp_yourpersonalaccesstoken

# Set permanently
setx GH_PACKAGE_LIBRARY_TOKEN "ghp_yourpersonalaccesstoken"

# Install packages
npm install
```

### Option 2: Local .npmrc Override

Create `.npmrc.local` (gitignored) with actual token:
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_yourpersonalaccesstoken
```

Then use:
```bash
npm install --userconfig .npmrc.local
```

### Option 3: Temporary Replacement (Not Recommended)

Temporarily replace the variable:
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=ghp_yourpersonalaccesstoken
```

⚠️ **Warning**: Remember to revert before committing!

## Creating a GitHub Personal Access Token

### Step 1: Access GitHub Settings
1. Go to GitHub.com and sign in
2. Click your profile picture → **Settings**
3. Scroll down and click **Developer settings**
4. Click **Personal access tokens** → **Tokens (classic)**

### Step 2: Generate New Token
1. Click **Generate new token** → **Generate new token (classic)**
2. Give it a descriptive name (e.g., "NPM Package Access")
3. Set expiration (recommended: 90 days)
4. Select scopes:
   - ✅ `read:packages` - Download packages from GitHub Packages
   - ✅ `write:packages` - Publish packages (if needed)
   - ✅ `repo` - Access private repositories (if packages are private)

### Step 3: Save the Token
1. Click **Generate token**
2. **Copy the token immediately** (you won't see it again!)
3. Store it securely (password manager recommended)

### Step 4: Configure npm
```bash
export GH_PACKAGE_LIBRARY_TOKEN=ghp_yourcopiedtoken
```

## Verifying Configuration

### Check Registry Configuration
```bash
npm config get @RoyalAholdDelhaize:registry
# Should output: https://npm.pkg.github.com
```

### Test Authentication
```bash
npm whoami --registry=https://npm.pkg.github.com
# Should output your GitHub username
```

### List Installed Configuration
```bash
npm config list
```

## Project-Level vs User-Level Configuration

### Project-Level (.npmrc in project root)
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${GH_PACKAGE_LIBRARY_TOKEN}
```
- Committed to repository
- Shared with all developers
- Uses environment variable

### User-Level (~/.npmrc)
```
//npm.pkg.github.com/:_authToken=ghp_yourpersonalaccesstoken
```
- Not committed
- Personal configuration
- Contains actual token

### Combining Both
The project `.npmrc` defines registry, while user `~/.npmrc` provides authentication:

**Project .npmrc:**
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```

**User ~/.npmrc:**
```
//npm.pkg.github.com/:_authToken=ghp_yourpersonalaccesstoken
```

## CI/CD Configuration

### GitHub Actions
```yaml
name: Install Dependencies
on: [push, pull_request]
jobs:
  install:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
        env:
          GH_PACKAGE_LIBRARY_TOKEN: ${{ secrets.GH_PACKAGE_LIBRARY_TOKEN }}
```

**Setup Secret:**
1. Go to repository Settings → Secrets and variables → Actions
2. Click **New repository secret**
3. Name: `GH_PACKAGE_LIBRARY_TOKEN`
4. Value: Your GitHub token
5. Click **Add secret**

### GitLab CI
```yaml
install:
  stage: install
  script:
    - npm ci
  variables:
    GH_PACKAGE_LIBRARY_TOKEN: $GH_PACKAGE_LIBRARY_TOKEN
```

**Setup Variable:**
1. Go to Settings → CI/CD → Variables
2. Add variable `GH_PACKAGE_LIBRARY_TOKEN`
3. Mark as **Protected** and **Masked**

## Troubleshooting

### 401 Unauthorized Error
```
npm ERR! code E401
npm ERR! 401 Unauthorized
```

**Solutions:**
1. Verify token is set: `echo $GH_PACKAGE_LIBRARY_TOKEN`
2. Check token has correct permissions (read:packages)
3. Ensure token hasn't expired
4. Generate new token and update configuration

### 404 Not Found Error
```
npm ERR! code E404
npm ERR! 404 Not Found
```

**Solutions:**
1. Verify package name is correct (check package.json)
2. Confirm you have access to the repository
3. Check package actually exists in GitHub Packages
4. Verify registry URL is correct

### Environment Variable Not Expanding
```
npm ERR! Invalid auth token
```

**Solutions:**
1. Ensure environment variable is exported
2. Restart terminal/IDE after setting variable
3. Check variable name matches exactly (case-sensitive)
4. Try hardcoding token temporarily to test

### Token in Wrong Format
```
npm ERR! Invalid authentication token
```

**Solutions:**
1. Token should start with `ghp_` (classic token)
2. Don't include quotes around token value
3. Ensure no extra spaces or newlines
4. Regenerate token if format seems wrong

## Security Best Practices

### 1. Never Commit Tokens
- Always use environment variables in committed files
- Add `.npmrc.local` to `.gitignore` if using local override
- Review files before committing: `git diff`

### 2. Use Minimal Permissions
- Only grant `read:packages` if you only need to download
- Avoid `repo` scope unless necessary for private packages

### 3. Rotate Tokens Regularly
- Set expiration dates on tokens
- Rotate every 90 days or when team members leave
- Document token rotation process

### 4. Use Different Tokens for Different Purposes
- Separate tokens for local development vs CI/CD
- Easier to revoke specific access
- Better audit trail

### 5. Store Tokens Securely
- Use password manager for personal tokens
- Use secrets management for CI/CD tokens
- Never share tokens in chat/email

## gitignore Recommendations

Add to `.gitignore`:
```
# NPM configuration with actual tokens
.npmrc.local
.npmrc.backup
*_backup.npmrc
```

## Team Setup Instructions

### For New Team Members

**Step 1: Create GitHub Token**
- Follow instructions in "Creating a GitHub Personal Access Token"
- Save token securely

**Step 2: Configure Environment**
```bash
# Add to ~/.zshrc or ~/.bashrc
export GH_PACKAGE_LIBRARY_TOKEN=ghp_yourtoken

# Reload configuration
source ~/.zshrc
```

**Step 3: Verify Setup**
```bash
npm whoami --registry=https://npm.pkg.github.com
```

**Step 4: Install Dependencies**
```bash
npm install
```

## Alternative: Using npm login

For some registries, you can use `npm login`:
```bash
npm login --registry=https://npm.pkg.github.com

Username: YOUR_GITHUB_USERNAME
Password: YOUR_GITHUB_TOKEN
Email: your@email.com
```

This creates an entry in `~/.npmrc` automatically.

## Migrating from Other Registries

### From Artifactory
```
# Old
@scope:registry=https://artifactory.company.com/api/npm/npm-local

# New
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```

### From Private npm Registry
```
# Old
registry=https://registry.company.com

# New (keep default registry, only scope specific packages)
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```

## Complete Setup Script

```bash
#!/bin/bash

# Setup script for new developers

echo "Setting up npm configuration for @RoyalAholdDelhaize packages"
echo ""

# Prompt for GitHub token
read -sp "Enter your GitHub Personal Access Token: " token
echo ""

# Export for current session
export GH_PACKAGE_LIBRARY_TOKEN=$token

# Add to shell profile
SHELL_RC="${HOME}/.zshrc"
if [ ! -f "$SHELL_RC" ]; then
    SHELL_RC="${HOME}/.bashrc"
fi

if ! grep -q "GH_PACKAGE_LIBRARY_TOKEN" "$SHELL_RC"; then
    echo "" >> "$SHELL_RC"
    echo "# GitHub Packages authentication" >> "$SHELL_RC"
    echo "export GH_PACKAGE_LIBRARY_TOKEN=$token" >> "$SHELL_RC"
    echo "✅ Added token to $SHELL_RC"
else
    echo "⚠️  Token already exists in $SHELL_RC"
fi

# Test authentication
echo ""
echo "Testing authentication..."
if npm whoami --registry=https://npm.pkg.github.com > /dev/null 2>&1; then
    echo "✅ Authentication successful!"
    echo ""
    echo "You can now run: npm install"
else
    echo "❌ Authentication failed. Please check your token."
fi
```

## Documentation Template for README

Add to your project's README.md:

```markdown
## Installing Dependencies

This project uses GitHub Packages for @RoyalAholdDelhaize scoped packages.

### First-Time Setup

1. Create a GitHub Personal Access Token with `read:packages` scope
2. Set the token as an environment variable:
   ```bash
   export GH_PACKAGE_LIBRARY_TOKEN=ghp_yourtoken
   ```
3. Add to your shell profile for persistence:
   ```bash
   echo 'export GH_PACKAGE_LIBRARY_TOKEN=ghp_yourtoken' >> ~/.zshrc
   ```

### Install
```bash
npm install
```

### Troubleshooting
If you get a 401 error, verify your token is set:
```bash
echo $GH_PACKAGE_LIBRARY_TOKEN
```
```
