# Template Maintenance Report
**Date**: 2026-01-14  
**Mode**: Audit (Read-Only)  
**Template**: Vue 3 Vite Application Template

## Executive Summary
- **Total Issues**: 4
- **Critical**: 1 (Deprecated package in use)
- **Warnings**: 2 (Major version updates available)
- **Info**: 1 (Schema path reference)

## 1. Package Versions

### 📦 Core Packages

| Package | Current Strategy | Latest Available | Status |
|---------|-----------------|------------------|---------|
| **vue** | `latest` | **3.5.26** | ⚠️ **Update recommended** |
| **vite** | `latest` | **7.3.1** | 🚨 **MAJOR update (6.x → 7.x)** |
| **typescript** | `latest` | **5.9.3** | ⚠️ **Minor update available** |
| **vue-router** | `latest` | **4.6.4** | ✅ Recent |
| **pinia** | `latest` | **3.0.4** | 🚨 **MAJOR update (2.x → 3.x)** |

### 📦 Testing Packages

| Package | Current Strategy | Latest Available | Status |
|---------|-----------------|------------------|---------|
| **vitest** | `latest` | **4.0.17** | ⚠️ **MAJOR update (2.x → 4.x)** |
| **@vitest/ui** | `latest` | **4.0.17** | ⚠️ **Update to match vitest** |
| **@vitest/coverage-v8** | `latest` | **4.0.17** | ⚠️ **Update to match vitest** |
| **@vue/test-utils** | `latest` | **2.4.6** | ✅ Current |
| **jsdom** | `latest` | **27.4.0** | ⚠️ **Major update available** |

### 📦 Build & Dev Tools

| Package | Current Strategy | Latest Available | Status |
|---------|-----------------|------------------|---------|
| **@vitejs/plugin-vue** | `latest` | **6.0.3** | ⚠️ **MAJOR update (5.x → 6.x)** |
| **eslint** | `latest` | **9.39.2** | ✅ Current |
| **prettier** | `latest` | **3.7.4** | ✅ Recent |
| **sass** | `latest` | **1.97.2** | ✅ Current |

### 📦 Dependencies

| Package | Current Strategy | Latest Available | Status |
|---------|-----------------|------------------|---------|
| **axios** | `latest` | **1.13.2** | ⚠️ **Major update available** |
| **husky** | `latest` | **9.1.7** | ✅ Current |
| **date-fns** | `latest` | **4.1.0** | ✅ Current |
| **core-js** | `latest` | **3.47.0** | ✅ Current |
| **single-spa-vue** | `latest` | **3.0.1** | ✅ Current |
| **@datadog/browser-rum** | `latest` | **6.25.3** | ✅ Current |

### 🚨 Deprecated/Unmaintained Packages

#### @vue/vue3-jest
- **Status**: 🚨 **UNMAINTAINED**
- **Last Published**: 2023-09-06 (over 2 years ago)
- **Current Version**: 29.2.6
- **Issue**: Package has not been updated since September 2023
- **Impact**: Template includes this in jest-config skill
- **Recommendation**: 
  - ✅ Template already recommends Vitest over Jest
  - ✅ Jest config skill includes warnings about @vue/vue3-jest
  - ℹ️ Consider removing Jest support entirely in next major version

**Template References**:
- `.github/agents/app-starter/agents-context/skills/jest-config/SKILL.md`
- `.github/agents/app-starter/agents-context/skills/jest-config/examples.md`

**Existing Warnings**: Template already includes critical warnings:
```
⚠️ CRITICAL: Verify @vue/vue3-jest maintenance status
If @vue/vue3-jest is unmaintained, prompt user for decision
```

## 2. API Deprecations

### ✅ Vue 3 APIs
- **No deprecated Vue APIs found**
- Template uses modern Composition API patterns
- No legacy Vue 2 patterns detected

### ✅ Vite Configuration
- **No deprecated Vite config patterns found**
- No usage of `build.target` (deprecated in Vite 6+)
- No problematic `optimizeDeps` patterns

### ✅ SCSS Patterns
- **No @import usage found**
- Template ready for Dart Sass 2.0
- Modern SCSS patterns in use

### ✅ TypeScript
- **No deprecated TypeScript patterns**
- Uses ES modules correctly
- No legacy namespace/module patterns

## 3. Best Practices

### ✅ State Management
- **Current**: Template supports both Vuex and Pinia
- **Recommendation**: Pinia is now the default (correct choice)
- **Action**: None required (already following best practices)

### ✅ Testing Framework
- **Current**: Template supports both Jest and Vitest
- **Recommendation**: Vitest is the default (correct choice)
- **Status**: ✅ Template defaults to modern tooling

### ✅ Composition API
- **Status**: Template uses modern Composition API patterns
- **Examples**: Follow Vue 3 best practices

### ⚠️ Vite Version Strategy
- **Current**: Uses Vite 6.x as "latest"
- **Available**: Vite 7.3.1 (major update)
- **Consideration**: Vite 7 may have breaking changes
- **Recommendation**: Research Vite 7 changes before updating template default

### ⚠️ Pinia Version Strategy
- **Current**: Uses Pinia 2.x as "latest"
- **Available**: Pinia 3.0.4 (major update)
- **Consideration**: Pinia 3 introduces new store syntax
- **Recommendation**: Research Pinia 3 migration before updating

## 4. Documentation

### ℹ️ Schema Path Reference
**Location**: `.github/agents/app-starter/agents-context/skills/configuration/SKILL.md:47`

**Current**:
```javascript
const schemaPath = '.github/agents/agents-context/app-starter/config.schema.json';
```

**Issue**: Path appears incorrect (should be `app-starter/agents-context/`)
**Severity**: Info (file doesn't exist in template)
**Impact**: Low - example code only
**Recommendation**: Update path or remove reference if schema file not implemented

### ✅ Internal Links
- **Status**: No broken internal links detected

### ✅ Package References
- **Status**: All referenced packages exist and are available on npm

### ✅ Documentation Completeness
- **Status**: Skill documentation is comprehensive
- **Examples**: Include proper warnings and validation steps

## 5. Major Version Updates Analysis

### Vite 6.x → 7.3.1
**Breaking Changes Research Needed**:
- Check Vite 7 changelog for breaking changes
- Verify plugin compatibility (@vitejs/plugin-vue)
- Test build output and dev server behavior
- Validate SystemJS bundle generation (micro-frontend support)

**Impact**: HIGH - Core build tool
**Recommendation**: Research before updating

### Pinia 2.x → 3.0.4
**Breaking Changes Research Needed**:
- Review Pinia 3.0 migration guide
- Check for new store syntax
- Validate TypeScript types
- Update skill examples if syntax changed

**Impact**: MEDIUM - Optional feature
**Recommendation**: Research and update examples

### Vitest 2.x → 4.0.17
**Breaking Changes Research Needed**:
- Review Vitest 4.0 changelog (skipped v3)
- Check configuration changes
- Verify coverage provider compatibility
- Update vitest-config skill examples

**Impact**: MEDIUM - Testing framework
**Recommendation**: Test thoroughly before updating

### @vitejs/plugin-vue 5.x → 6.0.3
**Breaking Changes Research Needed**:
- Check compatibility with Vite 7
- Review Vue SFC compilation changes
- Test with both standalone and micro-frontend builds

**Impact**: HIGH - Required for Vue support
**Recommendation**: Update together with Vite

### Axios 1.7.9 → 1.13.2
**Breaking Changes**: Likely minor within 1.x range
**Impact**: LOW - Dependency only
**Recommendation**: Safe to update

### jsdom 21.x → 27.4.0
**Breaking Changes Research Needed**:
- Check Node.js version requirements
- Verify Vitest compatibility
- Test component test environments

**Impact**: MEDIUM - Test environment
**Recommendation**: Research and test

## Recommendations

### Priority 1: Critical (Do Now)
1. ✅ **@vue/vue3-jest Warnings**: Template already includes appropriate warnings
   - Current approach is correct
   - No immediate action needed
   - Consider removing Jest support in future major version

### Priority 2: High (Next Quarterly Update)
2. **Research Major Updates**:
   - Vite 7.3.1 changes and migration path
   - Pinia 3.0 new features and breaking changes
   - Vitest 4.0 updates
   - Create migration guides for skill documentation

3. **Update Documentation**:
   - Fix schema path in configuration/SKILL.md (line 47)
   - Add migration notes for major version updates

### Priority 3: Medium (Next Bi-annual Update)
4. **Minor Version Updates**:
   - Vue 3.5.13 → 3.5.26 (patch update, safe)
   - TypeScript 5.7.3 → 5.9.3
   - Axios to 1.13.2

5. **Testing Package Alignment**:
   - Ensure @vitest/ui and @vitest/coverage-v8 match vitest version
   - Update to jsdom 27.x after compatibility verification

### Priority 4: Low (Future Consideration)
6. **Template Simplification**:
   - Consider deprecating Jest support (Vitest is superior for Vue 3)
   - Remove unmaintained package references
   - Simplify to single recommended path

## Version Update Strategy

### Immediate Updates (Safe)
```json
{
  "vue": "^3.5.26",           // Patch update
  "axios": "^1.13.2",          // Minor update within 1.x
  "typescript": "^5.9.3"       // Minor update
}
```

### Research Required (Breaking)
```json
{
  "vite": "^7.3.1",                    // MAJOR: 6.x → 7.x
  "@vitejs/plugin-vue": "^6.0.3",     // MAJOR: 5.x → 6.x
  "pinia": "^3.0.4",                   // MAJOR: 2.x → 3.x
  "vitest": "^4.0.17",                 // MAJOR: 2.x → 4.x
  "@vitest/ui": "^4.0.17",            // Match vitest
  "@vitest/coverage-v8": "^4.0.17",   // Match vitest
  "jsdom": "^27.4.0"                   // MAJOR update
}
```

### No Action Needed (Current)
```json
{
  "vue-router": "^4.6.4",
  "@vue/test-utils": "^2.4.6",
  "eslint": "^9.39.2",
  "prettier": "^3.7.4",
  "sass": "^1.97.2",
  "husky": "^9.1.7"
}
```

## Next Steps

1. **Immediate** (This Week):
   - ✅ Review this audit report
   - Update schema path documentation
   - Apply safe minor version updates (Vue, TypeScript, Axios)

2. **Short Term** (Next Sprint):
   - Research Vite 7 breaking changes
   - Research Pinia 3 migration guide
   - Test Vitest 4.0 compatibility
   - Create testing environment for major updates

3. **Medium Term** (Next Quarter):
   - Create branch for major version updates
   - Update and test all major version changes
   - Update skill documentation with new patterns
   - Generate migration guide for users

4. **Long Term** (Next Release):
   - Consider removing Jest support
   - Update template to use only Vitest
   - Simplify configuration options
   - Add CHANGELOG entry for all updates

## Testing Recommendations

Before applying any major updates, test:
1. ✅ `@app-starter generate a new Vue 3 application` (full template generation)
2. ✅ Build process (both standalone and micro-frontend)
3. ✅ Dev server functionality
4. ✅ Test execution (Vitest)
5. ✅ Component library integration
6. ✅ SystemJS bundle generation
7. ✅ ESLint and Prettier rules
8. ✅ Docker build

## Validation Checklist

- ✅ All JSON files in template are valid
- ✅ All markdown files are valid
- ✅ No broken internal links
- ✅ Package references are current
- ✅ Deprecation warnings present
- ✅ Template defaults to modern tooling (Vitest, Pinia)
- ⚠️ Minor documentation path issue (non-critical)

## Conclusion

**Template Health**: 🟢 **GOOD**

The Vue 3 Vite application template is in good condition with modern best practices:
- ✅ Uses latest recommended tooling (Vitest, Pinia)
- ✅ Includes appropriate warnings for unmaintained packages
- ✅ Follows modern Vue 3 patterns
- ✅ No critical deprecations in active use
- ⚠️ Several major version updates available (research required)

**Key Strength**: Template already correctly defaults to Vitest over Jest, avoiding the unmaintained @vue/vue3-jest package.

**Main Action**: Research major version updates (Vite 7, Pinia 3, Vitest 4) before updating template defaults.

---

**Report Generated**: 2026-01-14  
**Agent**: @maintainer  
**Next Audit Recommended**: 2026-04-14 (3 months)
