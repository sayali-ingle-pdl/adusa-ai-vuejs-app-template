# Git Ignore Skill

## Purpose
Generate the `.gitignore` file for version control exclusions.

## Output
Create the file: `.gitignore`

## Template

```
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# Dependencies
node_modules
dist
dist-ssr
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Testing
coverage
*.lcov

# Environment
.env.local
.env.*.local

# Build
build/

# Misc
.cache
.temp
```

## Notes
- Excludes node_modules and build artifacts
- Keeps .vscode/extensions.json for recommended extensions
- Excludes local environment files
