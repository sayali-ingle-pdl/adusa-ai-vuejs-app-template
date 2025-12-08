# Standalone Configuration Updates - Implementation Summary

## Changes Made

Based on the observation that `pdl-fulfilment-cf-launcher-web` (standalone project) doesn't have `.env.standalone`, we've updated the template to properly handle standalone vs micro-frontend configurations.

## Files Updated

### 1. STANDALONE_VS_MICROFRONTEND.md (NEW)
✅ Created comprehensive documentation showing all differences between standalone and micro-frontend apps
- Configuration matrix
- Decision logic
- File generation rules
- Validation rules

### 2. AGENT_INSTRUCTIONS.md
✅ Added Step 6: Determine Conditional File Generation
```javascript
// Files to skip for standalone applications
const filesToGenerate = {
  '.env.standalone': userConfig.is_microfrontend,  // Only for micro-frontends
};

// Dependencies
const conditionalDependencies = {
  'single-spa-vue': userConfig.is_microfrontend,
};

// Environment variables
const conditionalEnvVars = {
  'VITE_LAUNCHER_APP_URL': userConfig.is_microfrontend,
};
```

✅ Added validation to auto-correct `enable_single_spa` for standalone apps
```javascript
if (userConfig.is_standalone && userConfig.enable_single_spa) {
  console.warn('⚠️  Warning: Standalone apps typically do not use single-spa.');
  userConfig.enable_single_spa = false;
}
```

### 3. skills/env-standalone/SKILL.md
✅ Updated with conditional logic
- Added note: "This file should ONLY be created for micro-frontend applications"
- Added conditional logic section
- Explained why standalone apps don't need this file

### 4. skills/main-entry/SKILL.md
✅ Split into two templates:
- **Micro-Frontend Template**: With single-spa lifecycle, style tag management
- **Standalone Template**: Simple Vue app initialization, no single-spa

### 5. skills/env-constants/SKILL.md
✅ Split into two templates:
- **Micro-Frontend**: Includes `launcherAppUrl` export
- **Standalone**: Excludes `launcherAppUrl` export

### 6. config.schema.json
✅ Updated `enable_single_spa` description
- Added note: "Should be false for standalone applications"

## Key Differences Identified

| Feature | Micro-Frontend | Standalone |
|---------|---------------|------------|
| `.env.standalone` | ✅ Create | ❌ Skip |
| `single-spa-vue` dependency | ✅ Include | ❌ Skip |
| `VITE_LAUNCHER_APP_URL` | ✅ Include | ❌ Skip |
| `launcherAppUrl` in EnvConsts | ✅ Export | ❌ Skip |
| main.ts template | single-spa lifecycle | Simple mount |
| index.html mount | `#single-spa-application:...` | `#app` |
| Build format | `system` (SystemJS) | `es` (ES modules) |
| Router base | `/scoped-path` | `/` (typically) |

## Agent Decision Flow

```
User sets application_type
    │
    ├─── "micro-frontend" ────┐
    │                         │
    │                         ▼
    │                   Create .env.standalone
    │                   Include single-spa-vue
    │                   Add VITE_LAUNCHER_APP_URL
    │                   Use main-microfrontend.ts
    │                   Export launcherAppUrl
    │                   Set enable_single_spa = true
    │                   Use #single-spa-application:... mount
    │                   Build format = "system"
    │
    └─── "standalone" ────────┐
                              │
                              ▼
                        Skip .env.standalone
                        Skip single-spa-vue
                        Skip VITE_LAUNCHER_APP_URL
                        Use main-standalone.ts
                        Skip launcherAppUrl
                        Set enable_single_spa = false
                        Use #app mount
                        Build format = "es"
```

## Example Configurations

### Micro-Frontend Configuration
```json
{
  "application_type": "micro-frontend",
  "router_base_path": "/omni-inventory",
  "enable_single_spa": true
}
```

**Generates**:
- ✅ `.env.standalone` file
- ✅ `single-spa-vue` in package.json
- ✅ `VITE_LAUNCHER_APP_URL` in .env.local and EnvConsts.ts
- ✅ main.ts with single-spa lifecycle
- ✅ index.html with `#single-spa-application:omni-inventory`

### Standalone Configuration
```json
{
  "application_type": "standalone",
  "router_base_path": "/",
  "enable_single_spa": false
}
```

**Generates**:
- ❌ No `.env.standalone` file
- ❌ No `single-spa-vue` in package.json
- ❌ No `VITE_LAUNCHER_APP_URL` anywhere
- ✅ main.ts without single-spa (simple mount)
- ✅ index.html with `#app`

## Validation & Auto-Correction

The agent now automatically validates and corrects inconsistent configurations:

```javascript
// If user mistakenly sets this for standalone:
{
  "application_type": "standalone",
  "enable_single_spa": true  // ⚠️ Inconsistent!
}

// Agent auto-corrects to:
{
  "application_type": "standalone",
  "enable_single_spa": false  // ✅ Corrected
}
// + Shows warning message to user
```

## Skills Modified

1. **env-standalone**: Conditional generation (micro-frontend only)
2. **main-entry**: Two templates (micro-frontend vs standalone)
3. **env-constants**: Two templates (with/without launcherAppUrl)

## Benefits

✅ **Accurate Standalone Generation**: Matches real-world standalone projects like pdl-fulfilment-cf-launcher-web
✅ **No Unnecessary Files**: Standalone apps don't get irrelevant micro-frontend files
✅ **Cleaner Dependencies**: Standalone apps don't include single-spa-vue
✅ **Simpler main.ts**: Standalone apps get straightforward entry point
✅ **Auto-Validation**: Prevents configuration mistakes
✅ **Clear Documentation**: Both developers and agents understand the differences

## Testing Checklist

To verify the implementation works correctly:

- [ ] Generate micro-frontend app → Verify `.env.standalone` exists
- [ ] Generate standalone app → Verify `.env.standalone` does NOT exist
- [ ] Check micro-frontend main.ts → Has single-spa lifecycle
- [ ] Check standalone main.ts → Simple createApp and mount
- [ ] Check micro-frontend EnvConsts.ts → Has launcherAppUrl
- [ ] Check standalone EnvConsts.ts → NO launcherAppUrl
- [ ] Check micro-frontend package.json → Has single-spa-vue
- [ ] Check standalone package.json → NO single-spa-vue
- [ ] Verify auto-correction when enable_single_spa conflicts with standalone

## Summary

The key observation you made—**no `.env.standalone` for standalone apps**—revealed a fundamental distinction that needed to be properly handled. We've now updated:

- ✅ 6 configuration/documentation files
- ✅ 3 skill templates
- ✅ Added conditional generation logic
- ✅ Added validation and auto-correction
- ✅ Created comprehensive documentation

The agent will now correctly generate different configurations based on `application_type`, ensuring standalone apps match the structure of real standalone projects like `pdl-fulfilment-cf-launcher-web`.
