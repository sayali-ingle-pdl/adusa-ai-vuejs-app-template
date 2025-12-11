# Git Ignore Configuration Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains the template and detailed examples.

## Basic Template

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

## What Each Section Does
### System Files
.DS_Store
- **Purpose**: Excludes macOS system file
- **Why**: Prevents macOS-specific metadata from being committed
- **Impact**: Keeps repository clean for cross-platform development
### Dependencies
node_modules
- **Purpose**: Excludes all npm/yarn/pnpm installed packages
- **Why**: Packages are defined in package.json and should be installed locally
- **Impact**: Reduces repository size significantly (can be 100s of MBs)
### Build Output
/dist
- **Purpose**: Excludes the main production build directory
- **Why**: Build artifacts are generated from source code
- **Impact**: Only source code is tracked, builds are created during deployment
- **Note**: Leading `/` means only root-level dist directory
### Local Environment Files
# local env files
.env.local
.env.*.local
- **Purpose**: Excludes environment-specific configuration files
- **Why**: Prevents sensitive data (API keys, tokens) from being committed
- **Impact**: Each developer maintains their own local configuration
- **Examples**: `.env.local`, `.env.development.local`, `.env.production.local`
### Log Files
# Log files
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
- **Purpose**: Excludes package manager debug and error logs
- **Why**: Logs are generated during errors and contain local environment info
- **Impact**: Prevents cluttering repository with debugging output
- **Note**: `*` matches any characters after the base name
### Editor Directories and Files
# Editor directories and files
.idea
.vscode
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?
- **`.idea`**: JetBrains IDEs (WebStorm, IntelliJ IDEA) configuration
- **`.vscode`**: Visual Studio Code workspace settings
- **`*.suo`**: Visual Studio user options file
- **`*.ntvs*`**: Node.js Tools for Visual Studio
- **`*.njsproj`**: Node.js project file for Visual Studio
- **`*.sln`**: Visual Studio solution file
- **`*.sw?`**: Vim swap files (`.swp`, `.swo`, etc.)
**Why**: Editor-specific settings should not be shared (personal preferences)
### Test Coverage
# Coverage
coverage
*.lcov
- **`coverage`**: Directory created by Jest, NYC, or other coverage tools
- **`*.lcov`**: LCOV format coverage reports
- **Why**: Coverage reports are generated during testing
- **Impact**: Keeps test artifacts out of version control
### Build Artifacts
# Build
dist-ssr
*.local
- **`dist-ssr`**: Vite SSR (Server-Side Rendering) build output
- **`*.local`**: Any files ending with `.local` (local-only configurations)
- **Why**: Build outputs and local configs should not be committed
- **Impact**: Only source files are tracked
### Environment Variables
# Environment
.env
- **Purpose**: Excludes base environment file
- **Why**: May contain sensitive default values or secrets
- **Impact**: Prevents accidentally committing API keys or credentials
- **Note**: Use `.env.example` with placeholder values instead
## Common Patterns Explained
### Wildcards
- **`*`**: Matches any characters
  - `*.log` matches `error.log`, `debug.log`, etc.
- **`?`**: Matches single character
  - `*.sw?` matches `.swp`, `.swo`, `.swn`
- **`**`**: Matches directories recursively
  - `**/temp` matches `temp`, `src/temp`, `src/components/temp`
### Leading Slash
- **`/dist`**: Matches only in root directory
- **`dist`**: Matches in any directory
### Trailing Slash
- **`node_modules/`**: Explicitly matches only directories
- **`node_modules`**: Matches files or directories
## Verifying .gitignore
### Check if a file is ignored
git check-ignore -v path/to/file
### List all ignored files
git status --ignored
### Force add an ignored file (use cautiously)
git add -f path/to/file
## Common Issues and Solutions
### Files Already Committed
If files were committed before adding to .gitignore:
# Remove from git but keep locally
git rm --cached filename
# Remove directory from git but keep locally
git rm -r --cached directory/
# Commit the removal
git commit -m "Remove ignored files from tracking"
### .gitignore Not Working
1. **Clear git cache**:
   ```bash
   git rm -r --cached .
   git add .
   git commit -m "Update .gitignore"
   ```
2. **Check for negation rules**: Ensure no `!` rules are overriding
3. **Verify file path**: Use `git check-ignore -v <file>` to debug
### Global .gitignore
For patterns that apply to all projects:
# Configure global gitignore
git config --global core.excludesfile ~/.gitignore_global
# Add to ~/.gitignore_global
echo ".DS_Store" >> ~/.gitignore_global
## Environment-Specific Configurations
### Development Only
# Development
.env.development.local
tmp/
temp/
*.tmp
### Testing
# Test
test-results/
__snapshots__/
*.test.local.ts
### CI/CD
# CI/CD
.github/workflows/*.local.yml
deployment-config.local.json
## Best Practices
### 1. Start Early
- Add .gitignore before first commit
- Prevents accidentally committing sensitive files
### 2. Use Templates
- GitHub provides templates for different languages/frameworks
- Customize based on your project needs
### 3. Document Custom Rules
- Add comments explaining non-obvious patterns
- Helps team understand why certain files are ignored
### 4. Review Periodically
- Update as project evolves
- Remove obsolete patterns
- Add new patterns for new tools
### 5. Commit .gitignore
- Always commit .gitignore to repository
- Ensures consistent behavior across team
### 6. Use .env.example
- Provide template for environment variables
- Include placeholder values, not real secrets
### 7. Don't Over-Ignore
- Only ignore generated or sensitive files
- Don't ignore files that should be shared
## Security Considerations
### Never Commit
- API keys and secrets
- Database credentials
- SSH private keys
- Authentication tokens
- Personal access tokens
### Always Review
# Check what will be committed
git status
# Review changes before commit
git diff --cached
# Check for sensitive data
git log --all --full-history -- path/to/sensitive/file
### If Secrets Are Committed
1. **Rotate credentials immediately**
2. **Remove from history**:
   ```bash
   git filter-branch --force --index-filter \
   "git rm --cached --ignore-unmatch path/to/file" \
   --prune-empty --tag-name-filter cat -- --all
   ```
3. **Use tools like BFG Repo-Cleaner or git-secrets**
4. **Force push** (coordinate with team)
## Integration with CI/CD
### GitHub Actions
Files are automatically ignored in workflows
### GitLab CI
May need to explicitly clean:
before_script:
  - rm -rf node_modules
  - npm ci
### Docker
Create `.dockerignore` with similar patterns:
node_modules
dist
.git
.env.local
*.log
## Related Files
### .gitattributes
For line ending normalization:
* text=auto
*.js text eol=lf
*.ts text eol=lf
*.vue text eol=lf
### .npmignore
For npm package publishing (opposite of .gitignore):
# Include only dist and essential files
src/
tests/
.github/
*.config.ts
## Tools and Resources
### Online Generators
- [gitignore.io](https://gitignore.io): Generate .gitignore files
- [GitHub templates](https://github.com/github/gitignore): Official templates
### Validation
- **Git command**: `git check-ignore -v <path>`
- **IDE plugins**: Many editors highlight ignored files
### Maintenance
- Review quarterly
- Update when adding new tools
- Clean up obsolete patterns
