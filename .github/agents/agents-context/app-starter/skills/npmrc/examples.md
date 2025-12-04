# NPM RC Configuration for Copilot

## Generated .npmrc File

**IMPORTANT**: This file only configures the registry. Authentication must be set up in user's global ~/.npmrc.

```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```

## Authentication Requirements

**Before generating project files**, verify the user has GitHub Package authentication configured:

### Check if token is configured:
```bash
npm config get //npm.pkg.github.com/:_authToken
```

### If token is NOT configured, instruct user to set it up:

npm config set //npm.pkg.github.com/:_authToken ghp_YOUR_GITHUB_TOKEN

This adds the token to `~/.npmrc` (user's home directory), which is NOT tracked in git.

## What Gets Generated

The project `.npmrc` file contains ONLY the registry configuration:
```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
```

**Do NOT include the auth token** in the project `.npmrc` file.

## How npm Resolves Authentication

When npm installs packages:
1. Reads project `.npmrc` → finds registry for @RoyalAholdDelhaize scope
2. Reads user `~/.npmrc` → finds auth token for that registry
3. Combines both to authenticate and download packages

## Setup Instructions for Users

### If Token is Missing - Show This Message:

```
⚠️  GitHub Package Registry Authentication Required

This project uses @RoyalAholdDelhaize packages from GitHub Packages.
Before running npm install, you need to configure authentication:

1. Create a GitHub Personal Access Token:
   - Go to: https://github.com/settings/tokens
   - Click "Generate new token (classic)"
   - Select scope: read:packages
   - Copy the generated token

2. Configure npm authentication (one-time setup):

   npm config set //npm.pkg.github.com/:_authToken ghp_YOUR_TOKEN_HERE

3. Verify authentication:
   npm whoami --registry=https://npm.pkg.github.com

After setup, run npm install again.
```

## Verify Authentication Before Install

**Copilot should check authentication before attempting npm install:**

```bash
# Check if token exists
npm config get //npm.pkg.github.com/:_authToken

# If empty or returns "undefined", show setup instructions above
```
