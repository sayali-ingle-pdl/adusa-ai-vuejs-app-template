# Design Token Extractor Skill

## Overview
Processes Figma design tokens and transforms them into usable formats (SCSS, CSS variables, TypeScript) for Vue applications, ensuring design system consistency across all generated components.

## Capabilities
- Extract design tokens from Figma styles and variables
- Transform tokens into multiple output formats
- Generate SCSS mixins and functions
- Create TypeScript type definitions
- Build CSS custom properties with fallbacks
- Generate documentation for design tokens

## Usage
Automatically processes design tokens from parsed Figma files and generates standardized token files for use in component styling and theming.

## Key Functions
- `extractTokensFromFigma()` - Parses Figma styles into token objects
- `generateSCSSTokens()` - Creates SCSS variable files
- `generateCSSCustomProperties()` - Creates CSS custom property files
- `generateTypeScriptTokens()` - Creates typed token definitions
- `createTokenDocumentation()` - Generates token usage documentation

## Token Categories
- **Colors** - Brand colors, semantic colors, gradients
- **Typography** - Font families, sizes, weights, line heights
- **Spacing** - Margins, paddings, gaps with consistent scale
- **Shadows** - Box shadows and elevation tokens
- **Border Radius** - Corner radius values for consistency
- **Breakpoints** - Responsive design breakpoint definitions

## Output Formats
- SCSS variables and maps
- CSS custom properties
- TypeScript interfaces and constants
- JSON configuration files
- Design token documentation