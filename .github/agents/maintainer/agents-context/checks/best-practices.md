# Best Practices Check

## Purpose
Identify outdated patterns and suggest modern best practices based on current Vue/Vite ecosystem recommendations.

## Target Files
- All skill `examples.md` and `SKILL.md` files
- Configuration examples

## Best Practice Categories

### 1. Vue 3 Composition API Patterns

**Check**: Script setup syntax usage
```typescript
const COMPOSITION_PATTERNS = {
  'old-setup': {
    pattern: /setup\(\)\s*\{/,
    message: 'Consider using <script setup> for cleaner syntax',
    example: '<script setup lang="ts">',
    severity: 'info',
    benefits: ['Less boilerplate', 'Better TypeScript inference', 'Auto-imported components']
  },
  
  'ref-reactive-usage': {
    check: (code) => {
      // Prefer ref for primitives, reactive for objects
      const unnecessaryReactive = /reactive\(\s*['"]\w+['"]\s*\)/;
      return unnecessaryReactive.test(code);
    },
    message: 'Use ref() for primitives, reactive() for objects',
    severity: 'info'
  },
  
  'defineProps-typing': {
    pattern: /defineProps\(\{[^}]*type:\s*Object/,
    message: 'Use TypeScript interfaces for better type safety',
    example: 'interface Props { user: User }; defineProps<Props>()',
    severity: 'info'
  }
};
```

### 2. State Management Patterns

**Check**: Pinia store composition
```typescript
const STATE_PATTERNS = {
  'setup-stores': {
    check: (code) => {
      // Recommend setup stores over options stores for Pinia
      const isOptionsStore = /defineStore\([^,]+,\s*\{[^}]*state:/;
      return isOptionsStore.test(code);
    },
    message: 'Consider using setup stores for better TypeScript support',
    example: 'defineStore("user", () => { const state = ref(...); return { state }; })',
    severity: 'info'
  }
};
```

### 3. Testing Best Practices

**Check**: Test structure and assertions
```typescript
const TEST_PATTERNS = {
  'wrapper-vm': {
    pattern: /wrapper\.vm\./,
    message: 'Avoid accessing wrapper.vm, test public API instead',
    example: 'wrapper.find("button").trigger("click")',
    severity: 'warning'
  },
  
  'snapshot-overuse': {
    check: (code) => {
      const snapshots = (code.match(/toMatchSnapshot/g) || []).length;
      const totalTests = (code.match(/it\(|test\(/g) || []).length;
      return totalTests > 0 && snapshots / totalTests > 0.5;
    },
    message: 'Consider reducing snapshot tests, prefer explicit assertions',
    severity: 'info'
  }
};
```

### 4. Performance Patterns

**Check**: Optimization opportunities
```typescript
const PERFORMANCE_PATTERNS = {
  'computed-vs-method': {
    check: (code) => {
      // Look for methods that return computed values
      const methodReturningComputation = /methods:\s*\{[^}]*return\s+this\.\w+\.\w+/;
      return methodReturningComputation.test(code);
    },
    message: 'Consider using computed properties instead of methods for derived data',
    severity: 'info'
  },
  
  'v-for-with-key': {
    pattern: /v-for="[^"]*"(?![^>]*:key)/,
    message: 'Always use :key with v-for for performance',
    severity: 'warning'
  },
  
  'watch-immediate': {
    pattern: /watch\([^,]+,\s*\([^)]*\)\s*=>\s*\{/,
    message: 'Consider adding { immediate: true } if watcher should run on mount',
    severity: 'info'
  }
};
```

### 5. TypeScript Patterns

**Check**: Type safety improvements
```typescript
const TYPESCRIPT_PATTERNS = {
  'any-usage': {
    pattern: /:\s*any\b/,
    message: 'Avoid using "any", use specific types or "unknown"',
    severity: 'warning',
    exception: '// Exception: Sometimes necessary for 3rd party libs'
  },
  
  'non-null-assertion': {
    pattern: /!\s*\./,
    message: 'Avoid non-null assertion (!.), use optional chaining (?.) or type guards',
    severity: 'info'
  },
  
  'enum-vs-union': {
    pattern: /enum\s+\w+/,
    message: 'Consider string literal unions instead of enums for better tree-shaking',
    example: 'type Status = "pending" | "success" | "error"',
    severity: 'info'
  }
};
```

### 6. Vite Configuration

**Check**: Modern Vite patterns
```typescript
const VITE_PATTERNS = {
  'import-meta-env': {
    check: (code) => {
      const hasProcessEnv = /process\.env\.VITE_/;
      return hasProcessEnv.test(code);
    },
    message: 'Use import.meta.env instead of process.env in Vite projects',
    example: 'import.meta.env.VITE_API_URL',
    severity: 'warning'
  },
  
  'dynamic-imports': {
    check: (code) => {
      const hasSyncImports = /import\s+.*\s+from\s+['"].*\.vue['"]/;
      const isDynamic = /defineAsyncComponent|import\(/;
      return hasSyncImports.test(code) && !isDynamic.test(code);
    },
    message: 'Consider lazy loading components with defineAsyncComponent',
    severity: 'info'
  }
};
```

## Check Process

### Step 1: Load Best Practice Rules
Load all pattern definitions (can be extended with custom rules).

### Step 2: Scan Files
For each skill example file:

```javascript
function checkBestPractices(filePath, content) {
  const findings = [];
  
  // Extract code blocks
  const codeBlocks = extractCodeBlocks(content);
  
  for (const block of codeBlocks) {
    // Check each pattern category
    findings.push(...checkPatterns(COMPOSITION_PATTERNS, block));
    findings.push(...checkPatterns(STATE_PATTERNS, block));
    findings.push(...checkPatterns(TEST_PATTERNS, block));
    findings.push(...checkPatterns(PERFORMANCE_PATTERNS, block));
    findings.push(...checkPatterns(TYPESCRIPT_PATTERNS, block));
    findings.push(...checkPatterns(VITE_PATTERNS, block));
  }
  
  return findings;
}
```

### Step 3: Categorize Findings
Group by:
- Category (Composition API, Testing, Performance, etc.)
- Severity (critical, warning, info)
- File location

### Step 4: Generate Recommendations
For each finding, provide:
- What the issue is
- Why it matters
- How to fix it
- Benefits of the fix

## Output Format

### Console Output
```
💡 Best Practices Audit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Composition API Patterns:
  
  ℹ️ api-pattern/examples.md (line 156)
  Pattern: Old setup() syntax
  Recommendation: Use <script setup> for cleaner code
  Current:
    export default defineComponent({
      setup() {
        const count = ref(0);
        return { count };
      }
    });
  Better:
    <script setup lang="ts">
    const count = ref(0);
    </script>
  Benefits: Less boilerplate, better TypeScript inference

TypeScript Patterns:
  
  ⚠️ state-management/examples.md (line 89)
  Pattern: Usage of "any" type
  Recommendation: Use specific types for better type safety
  Found: const data: any = await response.json();
  Better: interface ApiResponse { ... }
          const data: ApiResponse = await response.json();

Performance Patterns:
  
  ℹ️ view-components/examples.md (line 234)
  Pattern: v-for without :key
  Recommendation: Always use :key with v-for
  Found: <li v-for="item in items">
  Better: <li v-for="item in items" :key="item.id">
  Impact: Better rendering performance and list diffing

Testing Patterns:
  ✅ All testing examples follow current best practices

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary:
  ⚠️ Warnings: 1
  ℹ️ Info: 2
  ✅ Categories with no issues: 3
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Recommendations:
  1. Update TypeScript patterns to avoid "any" type (1 occurrence)
  2. Consider modernizing setup() syntax to <script setup> (1 occurrence)
  3. Add :key to v-for directives (1 occurrence)

Note: These are suggestions for improving code quality. None are breaking changes.
```

### Report Data Structure
```typescript
interface BestPracticeFinding {
  file: string;
  line: number;
  category: string;
  pattern: string;
  message: string;
  current: string;
  better: string;
  benefits?: string[];
  severity: 'warning' | 'info';
  updateable: boolean;
}

interface BestPracticesReport {
  timestamp: string;
  findings: BestPracticeFinding[];
  summary: {
    warnings: number;
    info: number;
    categoriesChecked: number;
    categoriesClean: number;
  };
  recommendations: string[];
}
```

## Update Process

**Note**: Best practice updates are typically **manual** because they require understanding context and intent. The agent should:

1. **Report findings** clearly
2. **Explain benefits** of modern patterns
3. **Provide examples** of better approach
4. **Let user decide** whether to update

For **simple, safe patterns** (like adding `:key`), agent can offer to update:

```
Found 3 v-for directives without :key. These can be safely updated.

Update automatically? (yes/no) [no]:
```

## Evolution Tracking

Best practices change over time. Track when rules were added:

```typescript
interface BestPracticeRule {
  name: string;
  pattern: RegExp | ((code: string) => boolean);
  message: string;
  severity: 'warning' | 'info';
  addedDate: string;
  ecosystemVersion: string; // e.g., "Vue 3.4+", "Vite 5+"
  references?: string[]; // Links to docs/blog posts
}
```

This helps users understand if a recommendation is brand new or long-standing.

## Custom Rules

Users can add custom best practice rules:

**File**: `.github/agents/maintainer/custom-best-practices.json`
```json
{
  "customRules": [
    {
      "name": "Company-specific component naming",
      "pattern": "^(?!App)[A-Z]",
      "message": "Component names should start with 'App' prefix",
      "severity": "warning",
      "category": "Naming Conventions"
    }
  ]
}
```

## Exclusions

Some patterns should be excluded from checks:

```typescript
const EXCLUSIONS = {
  intentionalExamples: [
    '// Example: Bad pattern',
    '// Anti-pattern',
    '// Before migration',
    '// Vue 2 syntax'
  ],
  thirdPartyCode: [
    '// From library documentation',
    '// Third-party example'
  ]
};
```

## Testing

Validate rules:
1. True positives: Flag actual outdated patterns
2. True negatives: Ignore modern code
3. No false positives: Don't flag intentional examples
4. Context awareness: Understand migration guides vs recommendations
