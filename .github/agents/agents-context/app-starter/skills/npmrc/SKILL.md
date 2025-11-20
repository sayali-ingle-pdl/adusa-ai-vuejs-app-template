# NPM RC Skill

## Purpose
Generate the `.npmrc` file for npm package registry configuration.

## Output
Create the file: `.npmrc`

## Template

```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${GH_PACKAGE_LIBRARY_TOKEN}
```

## Notes
- Configures GitHub Packages registry for @RoyalAholdDelhaize scope
- Uses environment variable for authentication token
- Developers need to replace ${GH_PACKAGE_LIBRARY_TOKEN} with their personal access token locally
- After npm install, developers should revert changes to avoid committing tokens
