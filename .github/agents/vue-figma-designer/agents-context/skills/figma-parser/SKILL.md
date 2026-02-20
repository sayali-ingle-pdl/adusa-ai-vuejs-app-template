# Figma Parser Skill

## Overview
Extracts design specifications, components, and assets from Figma URLs using the Figma REST API. Processes design tokens, component hierarchies, and visual assets for Vue component generation.

## Capabilities
- Parse Figma files and extract component trees
- Extract design tokens (colors, typography, spacing, shadows)
- Download and optimize image assets
- Map component variants and properties
- Analyze layout structures and constraints
- Generate component specifications for Vue generation

## Usage
This skill is automatically triggered when a Figma URL is provided. It handles authentication, rate limiting, and data transformation.

## Key Functions
- `parseFigmaFile()` - Main entry point for parsing Figma files
- `extractDesignTokens()` - Processes design tokens from styles
- `processComponentHierarchy()` - Maps component structure
- `optimizeAssets()` - Downloads and processes images
- `generateComponentSpecs()` - Creates Vue component specifications

## Output
Returns structured data including:
- Component hierarchy and specifications
- Design tokens in multiple formats
- Optimized assets and metadata
- Layout and styling information
- Interaction and animation data

## Error Handling
- Rate limiting with exponential backoff
- Network error recovery
- Invalid URL validation
- Missing component graceful degradation