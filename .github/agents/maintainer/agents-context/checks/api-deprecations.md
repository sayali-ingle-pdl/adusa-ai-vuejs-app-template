# API Deprecations Check

## Purpose
Identify deprecated Vue, Vite, and ecosystem APIs in template examples and skill instructions.

## Target Files
- `.github/agents/app-starter/agents-context/skills/*/examples.md`
- `.github/agents/app-starter/agents-context/skills/*/SKILL.md`

## Known Deprecations Database

### Vue 3 API Deprecations
```typescript
const VUE_DEPRECATIONS = {
  // Composition API
  '@vue/composition-api': {
    pattern: /import .* from ['"]@vue\/composition-api['"]/,
    message: 'Vue 3 has built-in Composition API, remove this package',
    replacement: "import { ref, computed } from 'vue'",
    severity: 'warning'
  },
  
  // Filters (removed in Vue 3)
  filters: {
    pattern: /filters:\s*\{/,
    message: 'Filters removed in Vue 3, use computed properties or methods',
    severity: 'critical'
  }
};
```

### Vite Configuration Deprecations
```typescript
const VITE_DEPRECATIONS = {
  // Vite 5 → 6
  'build.target': {
    pattern: /build:\s*\{[^}]*target:/,
    message: 'build.target deprecated in Vite 6',
    replacement: 'Use build.modulePreload instead',
    since: 'Vite 6.0',
    severity: 'warning'
  },
  
  'optimizeDeps.exclude': {
    pattern: /optimizeDeps:\s*\{[^}]*exclude:/,
    message: 'Consider using optimizeDeps.needsInterop instead',
    since: 'Vite 5.0',
    severity: 'info'
  }
};
```

### SCSS/CSS Deprecations
```typescript
const SCSS_DEPRECATIONS = {
  '@import': {
    pattern: /@import\s+['"]/,
    message: '@import deprecated with Dart Sass, use @use/@forward',
    replacement: '@use',
    severity: 'warning',
    context: 'Dart Sass 2.0 removes @import support'
  },
  
  '/deep/': {
    pattern: /\/deep\//,
    message: '/deep/ is deprecated, use ::v-deep()',
    replacement: '::v-deep()',
    severity: 'warning'
  },
  
  '>>>': {
    pattern: />>>/,
    message: '>>> is deprecated, use ::v-deep()',
    replacement: '::v-deep()',
    severity: 'warning'
  },
  
  'division with /': {
    pattern: /\$[\w-]+\s*\/\s*\d+/,
    message: 'Division with / deprecated, use math.div()',
    replacement: '@use "sass:math"; math.div($var, 2)',
    severity: 'warning'
  }
};
```

### TypeScript Deprecations
```typescript
const TS_DEPRECATIONS = {
  'namespace': {
    pattern: /namespace\s+\w+/,
    message: 'Namespaces discouraged, use ES modules',
    severity: 'info'
  },
  
  'module': {
    pattern: /module\s+\w+/,
    message: 'Use ES modules instead of TS modules',
    severity: 'info'
  }
};
```

### Pinia/Vuex Patterns
```typescript
const STATE_MANAGEMENT_DEPRECATIONS = {
  'vuex-class': {
    pattern: /import .* from ['"]vuex-class['"]/,
    message: 'vuex-class not needed with Composition API, use useStore()',
    severity: 'info'
  }
};
```

### Testing Deprecations
```typescript
const TEST_DEPRECATIONS = {
  '@vue/test-utils createLocalVue': {
    pattern: /createLocalVue/,
    message: 'createLocalVue removed in Vue 3, use global config',
    replacement: 'mount(Component, { global: { plugins: [...] } })',
    severity: 'critical'
  },
  
  'wrapper.find().element': {
    pattern: /\.find\([^)]+\)\.element/,
    message: 'Consider using .get() for assertions (throws if not found)',
    severity: 'info'
  }
};
```

## Check Process

### Step 1: Scan All Example Files
```bash
find .github/agents/app-starter/agents-context/skills/ -name "examples.md" -type f
```

### Step 2: Pattern Matching
For each file, search for deprecated patterns:

```javascript
function checkFile(filePath, content) {
  const findings = [];
  
  // Check Vue deprecations
  for (const [name, dep] of Object.entries(VUE_DEPRECATIONS)) {
    const matches = content.matchAll(new RegExp(dep.pattern, 'gm'));
    for (const match of matches) {
      findings.push({
        file: filePath,
        line: getLineNumber(content, match.index),
        deprecation: name,
        message: dep.message,
        replacement: dep.replacement,
        severity: dep.severity
      });
    }
  }
  
  // Check Vite deprecations
  // Check SCSS deprecations
  // Check TypeScript deprecations
  // etc.
  
  return findings;
}
```

### Step 3: Context Analysis
For each finding, extract surrounding code:

```javascript
function getContext(content, lineNumber, contextLines = 3) {
  const lines = content.split('\n');
  const start = Math.max(0, lineNumber - contextLines);
  const end = Math.min(lines.length, lineNumber + contextLines + 1);
  
  return lines.slice(start, end).join('\n');
}
```

## Output Format

### Console Output
```
🔧 API Deprecation Audit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SCSS Patterns (2 issues):
  
  ⚠️ api-pattern/examples.md (line 28)
  Issue: @import deprecated with Dart Sass
  Found: @import '@/theme/vars.scss';
  Replace: @use '@/theme' as *;
  Context:
    25: <style scoped lang="scss">
    26: // Old pattern (deprecated)
    27: @import '@/theme/vars.scss';
    28: 
    29: .component {
  
  ⚠️ view-components/examples.md (line 45)
  Issue: @import deprecated with Dart Sass
  Found: @import '@/theme';
  Replace: @use '@/theme' as *;

Vite Configuration (1 issue):
  
  ⚠️ vite-config/examples.md (line 67)
  Issue: build.target deprecated in Vite 6
  Found: target: 'es2020'
  Replace: Use build.modulePreload configuration instead
  Since: Vite 6.0
  Context:
    65: build: {
    66:   lib: {
    67:     target: 'es2020',
    68:     formats: ['system']
    69:   }

Vue Test Utils (0 issues):
  ✅ No deprecated patterns found

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary:
  🚨 Critical: 0
  ⚠️ Warnings: 3
  ℹ️ Info: 0
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Recommendations:
  1. Update SCSS patterns to use @use instead of @import (2 files)
  2. Update Vite config examples for Vite 6 (1 file)
```

### Report Data Structure
```typescript
interface DeprecationFinding {
  file: string;
  line: number;
  column?: number;
  deprecation: string;
  pattern: string;
  message: string;
  replacement?: string;
  severity: 'critical' | 'warning' | 'info';
  context: string;
  since?: string;
}

interface APIDeprecationReport {
  timestamp: string;
  findings: DeprecationFinding[];
  summary: {
    critical: number;
    warnings: number;
    info: number;
    filesAffected: number;
  };
  recommendations: string[];
}
```

## Update Process

When user approves API deprecation updates:

### Step 1: Confirm Changes
Show user exact replacements:
```
Update SCSS @import patterns? (2 occurrences)

File: api-pattern/examples.md (line 28)
  Before: @import '@/theme/vars.scss';
  After:  @use '@/theme' as *;

File: view-components/examples.md (line 45)
  Before: @import '@/theme';
  After:  @use '@/theme' as *;

Apply these changes? (yes/no) [yes]:
```

### Step 2: Apply Replacements
```javascript
function applyReplacement(filePath, finding) {
  let content = fs.readFileSync(filePath, 'utf-8');
  const lines = content.split('\n');
  
  // Replace specific line
  lines[finding.line - 1] = lines[finding.line - 1].replace(
    finding.pattern,
    finding.replacement
  );
  
  content = lines.join('\n');
  fs.writeFileSync(filePath, content);
}
```

### Step 3: Validate Changes
- Check syntax is still valid
- Verify no unintended replacements
- Ensure file is still parseable

### Step 4: Report Results
```
✅ Updated API Deprecations

Modified Files:
  - api-pattern/examples.md (1 change)
  - view-components/examples.md (1 change)
  - vite-config/examples.md (1 change)

Changes:
  - Replaced @import with @use (2 occurrences)
  - Updated Vite config pattern (1 occurrence)

Validation: ✅ All files valid
```

## Special Cases

### Code Blocks in Markdown
Only scan code blocks, not prose:

```javascript
function extractCodeBlocks(markdown) {
  const codeBlockRegex = /```[\w]*\n([\s\S]*?)```/g;
  const blocks = [];
  let match;
  
  while ((match = codeBlockRegex.exec(markdown)) !== null) {
    blocks.push({
      content: match[1],
      start: getLineNumber(markdown, match.index),
      language: match[0].match(/```(\w+)/)?.[1]
    });
  }
  
  return blocks;
}
```

Only check within code blocks for actual code deprecations.

### False Positives
Avoid flagging examples that intentionally show deprecated patterns:

```markdown
<!-- Don't flag this -->
## Migration Guide
Before (Vue 2 - deprecated):
```javascript
filters: { uppercase: (v) => v.toUpperCase() }
```

After (Vue 3):
```javascript
const uppercase = computed(() => value.toUpperCase())
```
```

**Detection**: Look for "deprecated", "before", "old", "legacy" keywords near pattern.

### Version-Specific Deprecations
Only flag if user's template targets the version where it's deprecated:

```javascript
if (deprecation.since === 'Vite 6.0') {
  // Check if template uses Vite 6+
  const viteVersion = getPackageVersion('vite');
  if (semver.gte(viteVersion, '6.0.0')) {
    // Flag it
  }
}
```

## Custom Deprecation Rules

Users can extend with custom rules:

**File**: `.github/agents/maintainer/custom-deprecations.json`
```json
{
  "customPatterns": [
    {
      "name": "Old component library import",
      "pattern": "import .* from '@old-org/components'",
      "message": "Use new component library",
      "replacement": "import { ... } from '@new-org/components'",
      "severity": "warning"
    }
  ]
}
```

## Testing

Before reporting, validate:
1. Pattern matching works correctly
2. Line numbers are accurate
3. Context extraction is helpful
4. No false positives in prose/comments
5. Replacements are safe and correct
