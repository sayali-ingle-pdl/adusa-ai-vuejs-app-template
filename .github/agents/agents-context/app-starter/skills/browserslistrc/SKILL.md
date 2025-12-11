# Browserslist Skill

## Purpose
Generate Browserslist configuration to define supported browsers for CSS autoprefixing and JavaScript transpilation.

## Output
Create the file: `.browserslistrc`

## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- Targets browsers with >1% market share
- Supports last 2 versions of all major browsers
- Excludes browsers without official support or security updates (dead browsers)
- Explicitly excludes Internet Explorer 11
- Used by Autoprefixer, Babel, and other build tools
- Helps optimize bundle size by not supporting outdated browsers
- Ensures modern browser features can be used with appropriate polyfills
