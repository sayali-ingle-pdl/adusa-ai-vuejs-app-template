# Git Ignore Skill

## Purpose
Generate the `.gitignore` file for version control exclusions.

## Output
Create the file: `.gitignore`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```
.DS_Store
node_modules
/dist

# local env files
.env.local
.env.*.local

# Log files
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*

# Editor directories and files
.idea
.vscode
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Coverage
coverage
*.lcov

# Build
dist-ssr
*.local

# Environment
.env
```

## Notes
- Excludes macOS system files (.DS_Store)
- Excludes node_modules and build artifacts (dist, dist-ssr)
- Excludes local environment files (.env.local, .env.*.local, .env)
- Excludes all editor directories (.vscode, .idea)
- Excludes log files from npm, yarn, and pnpm
- Excludes test coverage reports
- Excludes *.local files for local-only configurations
