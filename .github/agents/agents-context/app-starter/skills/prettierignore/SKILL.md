# Prettier Ignore Skill

## Purpose
Generate .prettierignore file to exclude files and directories from Prettier formatting.

## Output
Create the file: `.prettierignore`

## Notes
- Excludes build output directories (dist, build)
- Excludes dependencies (node_modules)
- Excludes test coverage reports
- Excludes minified and bundled JavaScript files
- Prevents Prettier from formatting generated or third-party code
- Improves formatting performance by skipping unnecessary files
- Uses glob patterns similar to .gitignore
