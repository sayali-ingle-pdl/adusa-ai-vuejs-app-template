# NPM RC Skill

## Purpose
Generate the `.npmrc` file for npm package registry configuration.

## Output
Create the file: `.npmrc`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```
@RoyalAholdDelhaize:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${GH_PACKAGE_LIBRARY_TOKEN}
```

## Notes
- Configures GitHub Packages registry for @RoyalAholdDelhaize scope
- Uses environment variable `${GH_PACKAGE_LIBRARY_TOKEN}` for authentication
- Token must be set as environment variable or collected from user input
- Users need to export GH_PACKAGE_LIBRARY_TOKEN before running npm install
- Alternative: Replace `${GH_PACKAGE_LIBRARY_TOKEN}` with actual token temporarily
- Never commit actual tokens to version control
- File can be committed with placeholder variable
