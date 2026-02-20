# Component Generator Skill

## Overview
Generates production-ready Vue 3 Single File Components (SFCs) from Figma design specifications using the Options API pattern with TypeScript support.

## Capabilities
- Generate Vue 3 SFCs with proper structure and organization
- TypeScript integration with strict type checking
- Props definition with validation and defaults
- Event handling and emission patterns
- Computed properties and watchers
- Lifecycle hook implementation
- SCSS styling with design token integration
- Accessibility attributes and ARIA support

## Usage
This skill processes component specifications from the Figma parser and generates complete Vue components following project conventions and best practices.

## Key Functions
- `generateVueComponent()` - Main component generation entry point
- `generateComponentScript()` - Creates the script section with Options API
- `generateComponentTemplate()` - Builds the template with proper structure
- `generateComponentStyles()` - Generates SCSS with design tokens
- `validateComponentStructure()` - Ensures component follows best practices

## Component Structure
Generated components follow the standard Vue SFC structure:
1. `<template>` - Semantic HTML with accessibility
2. `<script lang="ts">` - Options API with TypeScript
3. `<style lang="scss" scoped>` - Styled with design tokens

## Features
- Proper prop validation and typing
- Event emission with payload types
- Slot management with fallback content
- Responsive design integration
- Performance optimizations
- Testing hooks and data attributes

## Quality Assurance
- Vue 3 best practices compliance
- TypeScript strict mode compatibility
- Accessibility standards adherence
- Performance optimization patterns
- Code maintainability standards