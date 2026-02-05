# Package Versions Check

## Purpose
Identify outdated package versions in the template by comparing against npm registry.

## Target Files
- `.github/agents/app-starter/agents-context/skills/package-json/SKILL.md` (Version Configuration section)
- `.github/agents/app-starter/agents-context/skills/package-json/reference/latest-versions.md` (Template reference)

## Check Process

### Step 1: Read Version Configuration
```bash
cat .github/agents/app-starter/agents-context/skills/package-json/SKILL.md
```

Extract the "Version Configuration (versions.json)" section (lines ~120-209).

### Step 2: Extract Packages
Parse the JSON configuration block and extract all package names and current versions.

**Expected structure**:
```json
{
  "core": {
    "vue": "^3.5.13",
    "vite": "^6.3.5"
  },
  "typescript": {
    "typescript": "^5.7.3"
  },
  "testing": {
    "vitest": "^2.1.8",
    "@vue/test-utils": "^2.4.6"
  }
}
```

### Step 3: Fetch Latest Versions
For each package, query npm registry:

```bash
npm view <package-name> version
npm view <package-name> time
```

**Example**:
```bash
npm view vue version
# Output: 3.6.2

npm view vue time --json | jq -r '.modified'
# Output: 2026-01-10T14:23:45.123Z
```

### Step 4: Compare Versions
Use semver comparison:
- **Patch update**: 3.5.13 → 3.5.14 (low priority)
- **Minor update**: 3.5.13 → 3.6.0 (medium priority)
- **Major update**: 3.5.13 → 4.0.0 (high priority, breaking)

### Step 5: Check Maintenance Status
Flag packages as potentially unmaintained if:
- Last publish date > 1 year ago
- AND no major/minor updates in that time

**Known deprecated packages** (hardcoded list, update as needed):
```javascript
const DEPRECATED_PACKAGES = {
  '@vue/vue3-jest': {
    reason: 'Unmaintained, Vue 3 testing shifted to Vitest',
    alternative: 'vitest + @vue/test-utils',
    since: '2023-11'
  },
  'vue-jest': {
    reason: 'Vue 2 only',
    alternative: '@vue/test-utils + vitest',
    since: '2022-12'
  }
};
```

## Output Format

### Console Output
```
📦 Package Version Audit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Core Packages:
  vue
    Current: ^3.5.13
    Latest:  3.6.2
    Status:  ⚠️ Minor update available
    
  vite
    Current: ^6.3.5
    Latest:  6.3.5
    Status:  ✅ Up to date

TypeScript:
  typescript
    Current: ^5.7.3
    Latest:  5.7.3
    Status:  ✅ Up to date

Testing:
  vitest
    Current: ^2.1.8
    Latest:  2.2.0
    Status:  ⚠️ Minor update available
    
  @vue/test-utils
    Current: ^2.4.6
    Latest:  2.4.6
    Status:  ✅ Up to date
    
  @vue/vue3-jest
    Current: ^29.2.6
    Latest:  29.2.6
    Status:  🚨 DEPRECATED
    Reason:  Unmaintained since 2023-11
    Alternative: vitest + @vue/test-utils

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary:
  ✅ Up to date: 3 packages
  ⚠️ Updates available: 2 packages (minor)
  🚨 Deprecated: 1 package
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Recommendations:
  1. Update vue to ^3.6.2 (minor, safe)
  2. Update vitest to ^2.2.0 (minor, safe)
  3. Remove @vue/vue3-jest, use vitest instead (already supported)
```

### Report Data Structure
```typescript
interface PackageAudit {
  package: string;
  currentVersion: string;
  latestVersion: string;
  updateType: 'patch' | 'minor' | 'major' | 'none';
  isDeprecated: boolean;
  deprecationInfo?: {
    reason: string;
    alternative: string;
    since: string;
  };
  lastPublished: string;
  daysSincePublish: number;
}

interface PackageVersionReport {
  timestamp: string;
  packages: PackageAudit[];
  summary: {
    upToDate: number;
    patchUpdates: number;
    minorUpdates: number;
    majorUpdates: number;
    deprecated: number;
  };
  recommendations: string[];
}
```

## Update Process

When user approves package version updates:

### Step 1: Backup Current SKILL.md
```bash
cp .github/agents/app-starter/agents-context/skills/package-json/SKILL.md SKILL.md.backup
```

### Step 2: Update Versions
For each approved package, update the version configuration JSON block in SKILL.md:
```javascript
// Read current SKILL.md and extract JSON block
const skillContent = fs.readFileSync('SKILL.md', 'utf-8');
const jsonMatch = skillContent.match(/## Version Configuration.*?```json\n([\s\S]*?)\n```/);

// Parse and update JSON
const versions = JSON.parse(jsonMatch[1]);
versions.core.vue = 'latest'; // Keep as 'latest' or pin if needed

// Replace in SKILL.md
const updatedJson = JSON.stringify(versions, null, 2);
const updatedContent = skillContent.replace(jsonMatch[1], updatedJson);
fs.writeFileSync('SKILL.md', updatedContent);
```

### Step 3: Validate JSON Block
```bash
# Extract and validate the JSON block from SKILL.md
grep -A 100 '## Version Configuration' SKILL.md | grep -A 95 '```json' | tail -n +2 | head -n -1 | jq . > /dev/null
```
If validation fails, restore from backup.

### Step 4: Update Reference Files (if needed)
Check if `latest-versions.md` references need updating:
```bash
grep -r "\{\{vue_version\}\}" .github/agents/app-starter/agents-context/skills/package-json/reference/
```

Note: Reference files use placeholders, not hardcoded versions.

### Step 5: Report Changes
```
✅ Updated Package Versions

Modified: .github/agents/app-starter/agents-context/skills/package-json/SKILL.md
  - vue: latest (now resolves to ^3.6.2)
  - vitest: latest (now resolves to ^2.2.0)

Note: Versions remain as 'latest' to auto-fetch newest at generation time.

Validation: ✅ JSON block valid
```

## Special Cases

### "latest" Marker
If version is set to `"latest"`:
```json
{
  "core": {
    "vue": "latest"
  }
}
```

**Behavior**:
- Don't update to specific version
- Note in report that it will fetch latest at generation time
- Check what current latest is for user's awareness

### Major Version Updates
Always flag major updates for user review:
```
🚨 Major Version Update Detected

Package: @vitejs/plugin-vue
Current: ^5.2.1
Latest: 6.0.0

⚠️ This is a MAJOR version change and may include breaking changes.

Breaking Changes:
  - [List breaking changes if known]
  - Check: https://github.com/vitejs/vite-plugin-vue/releases/tag/v6.0.0

Recommendation: Review changelog before updating.
Update this package? (yes/no) [no]:
```

### Security Advisories
If npm reports security vulnerabilities:
```bash
npm audit --json
```

Flag in report:
```
🔒 Security Advisory

Package: vite
Current: ^5.0.0
Vulnerable: Yes
Severity: High
Patched: 5.0.13+

⚠️ CRITICAL: Update immediately for security fix
```

## Error Handling

**If npm registry unreachable**:
```
❌ Cannot fetch package versions
Reason: npm registry timeout
Action: Check internet connection, try again later
```

**If package not found**:
```
⚠️ Package not found: @example/package
Reason: Package may have been removed from npm
Action: Review if this package is still needed
```

**If version parsing fails**:
```
⚠️ Cannot parse version for: package-name
Current value: "invalid-version"
Action: Manually review SKILL.md Version Configuration section
```

## Testing

Before reporting to user, validate:
1. All packages exist in npm registry
2. Version comparison is correct
3. Deprecated package list is current
4. Recommendation logic is sound

**Test with known scenarios**:
- Package with patch update available
- Package with minor update available
- Package with major update available
- Deprecated package
- Up-to-date package
- Package with security vulnerability
