---
name: npmrc
description: Conditionally generates .npmrc for GitHub Packages registry configuration. Maps organization scope to registry endpoint when component library is requested. Token stored in ~/.npmrc only.
---

# NPM RC Skill

## Purpose
Generate the `.npmrc` file for npm package registry configuration.

## Output
Create the file: `.npmrc`


## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- Configures GitHub Packages registry for @RoyalAholdDelhaize scope
- **During initial setup**: Uses the `github_token` parameter collected during application setup
- The token will be directly inserted into .npmrc to enable the first `npm install`
- **After npm install**: The agent will replace the actual token with `${GH_PACKAGE_LIBRARY_TOKEN}` environment variable placeholder
- This two-step process allows successful initial installation while keeping the committed .npmrc safe
- Final committed version uses environment variable for security
- Users need to set `GH_PACKAGE_LIBRARY_TOKEN` environment variable for subsequent npm installs
- Never commit actual tokens to version control
