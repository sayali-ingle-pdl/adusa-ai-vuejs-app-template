# Git Ignore Skill

## Purpose
Generate the `.gitignore` file for version control exclusions.

## Output
Create the file: `.gitignore`

## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- Excludes macOS system files (.DS_Store)
- Excludes node_modules and build artifacts (dist, dist-ssr)
- Excludes local environment files (.env.local, .env.*.local, .env)
- Excludes all editor directories (.vscode, .idea)
- Excludes log files from npm, yarn, and pnpm
- Excludes test coverage reports
- Excludes *.local files for local-only configurations
