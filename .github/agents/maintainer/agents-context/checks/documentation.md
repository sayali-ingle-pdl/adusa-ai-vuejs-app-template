# Documentation Check

## Purpose
Validate documentation accuracy by checking links, references, and consistency across template files.

## Target Files
- All `.md` files in the template
- Specifically:
  - `config/README.md`
  - `QUICK_START.md`
  - `docs/requirements/application-parameters.md`
  - `.github/agents/app-starter.agent.md`
  - All skill `SKILL.md` and `examples.md` files

## Check Categories

### 1. Link Validation

**Internal Links**:
```javascript
function checkInternalLinks(filePath, content) {
  const linkRegex = /\[([^\]]+)\]\(([^)]+)\)/g;
  const findings = [];
  
  for (const match of content.matchAll(linkRegex)) {
    const [, text, url] = match;
    
    // Skip external URLs
    if (url.startsWith('http://') || url.startsWith('https://')) {
      continue;
    }
    
    // Resolve relative path
    const absolutePath = path.resolve(path.dirname(filePath), url);
    
    // Check if file exists
    if (!fs.existsSync(absolutePath)) {
      findings.push({
        file: filePath,
        line: getLineNumber(content, match.index),
        type: 'broken-link',
        link: url,
        text: text,
        severity: 'warning'
      });
    }
  }
  
  return findings;
}
```

**External Links**:
```javascript
async function checkExternalLinks(filePath, content) {
  const linkRegex = /\[([^\]]+)\]\((https?:\/\/[^)]+)\)/g;
  const findings = [];
  
  for (const match of content.matchAll(linkRegex)) {
    const [, text, url] = match;
    
    try {
      const response = await fetch(url, { method: 'HEAD', timeout: 5000 });
      
      if (response.status >= 400) {
        findings.push({
          file: filePath,
          line: getLineNumber(content, match.index),
          type: 'broken-external-link',
          link: url,
          status: response.status,
          severity: 'warning'
        });
      }
    } catch (error) {
      findings.push({
        file: filePath,
        line: getLineNumber(content, match.index),
        type: 'unreachable-link',
        link: url,
        error: error.message,
        severity: 'info'
      });
    }
  }
  
  return findings;
}
```

### 2. Package References

**Check**: References to packages still exist and are current

```javascript
function checkPackageReferences(content) {
  const packageRegex = /`(@?[\w-]+\/[\w-]+|@[\w-]+|[\w-]+)`/g;
  const findings = [];
  
  const knownPackages = new Set();
  
  for (const match of content.matchAll(packageRegex)) {
    const packageName = match[1];
    
    // Skip if already checked
    if (knownPackages.has(packageName)) continue;
    knownPackages.add(packageName);
    
    // Check if package exists on npm
    try {
      const info = await exec(`npm view ${packageName} name`);
      // Package exists
    } catch (error) {
      findings.push({
        type: 'missing-package',
        package: packageName,
        message: 'Package not found on npm registry',
        severity: 'warning'
      });
    }
  }
  
  return findings;
}
```

### 3. Version Consistency

**Check**: Version references match versions.json

```javascript
function checkVersionConsistency(allFiles, versionsJson) {
  const findings = [];
  
  // Extract version references from docs
  const versionRegex = /(\w+)@?([\d.]+)/g;
  
  for (const file of allFiles) {
    for (const match of file.content.matchAll(versionRegex)) {
      const [, packageName, docVersion] = match;
      
      // Look up in versions.json
      const actualVersion = getPackageVersion(versionsJson, packageName);
      
      if (actualVersion && actualVersion !== docVersion && actualVersion !== 'latest') {
        findings.push({
          file: file.path,
          type: 'version-mismatch',
          package: packageName,
          documentedVersion: docVersion,
          actualVersion: actualVersion,
          severity: 'info'
        });
      }
    }
  }
  
  return findings;
}
```

### 4. Code Example Validation

**Check**: Code examples are syntactically valid

```javascript
function validateCodeExamples(content) {
  const findings = [];
  const codeBlocks = extractCodeBlocks(content);
  
  for (const block of codeBlocks) {
    if (block.language === 'typescript' || block.language === 'javascript') {
      try {
        // Basic syntax check (not full type checking)
        new Function(block.content);
      } catch (error) {
        findings.push({
          type: 'invalid-code',
          language: block.language,
          line: block.start,
          error: error.message,
          severity: 'warning'
        });
      }
    }
    
    if (block.language === 'json') {
      try {
        JSON.parse(block.content);
      } catch (error) {
        findings.push({
          type: 'invalid-json',
          line: block.start,
          error: error.message,
          severity: 'warning'
        });
      }
    }
  }
  
  return findings;
}
```

### 5. Cross-Reference Validation

**Check**: References between files are accurate

```javascript
function checkCrossReferences(allFiles) {
  const findings = [];
  
  // Build index of all headings
  const headingIndex = {};
  for (const file of allFiles) {
    const headings = file.content.match(/^#+\s+(.+)$/gm) || [];
    headingIndex[file.path] = headings.map(h => h.replace(/^#+\s+/, ''));
  }
  
  // Check anchor links
  const anchorRegex = /\[([^\]]+)\]\(([^)#]+)#([^)]+)\)/g;
  
  for (const file of allFiles) {
    for (const match of file.content.matchAll(anchorRegex)) {
      const [, text, targetFile, anchor] = match;
      
      const resolvedPath = path.resolve(path.dirname(file.path), targetFile);
      const headings = headingIndex[resolvedPath] || [];
      
      const anchorText = anchor.replace(/-/g, ' ').toLowerCase();
      const headingExists = headings.some(h => 
        h.toLowerCase() === anchorText
      );
      
      if (!headingExists) {
        findings.push({
          file: file.path,
          type: 'broken-anchor',
          link: `${targetFile}#${anchor}`,
          severity: 'warning'
        });
      }
    }
  }
  
  return findings;
}
```

### 6. Documentation Completeness

**Check**: All skills have required documentation

```javascript
function checkDocumentationCompleteness(skillsDir) {
  const findings = [];
  const skills = fs.readdirSync(skillsDir);
  
  for (const skill of skills) {
    const skillPath = path.join(skillsDir, skill);
    
    // Required files
    const requiredFiles = ['SKILL.md', 'examples.md'];
    
    for (const file of requiredFiles) {
      const filePath = path.join(skillPath, file);
      
      if (!fs.existsSync(filePath)) {
        findings.push({
          skill: skill,
          type: 'missing-documentation',
          file: file,
          severity: 'warning'
        });
      }
    }
    
    // Check SKILL.md has required sections
    if (fs.existsSync(path.join(skillPath, 'SKILL.md'))) {
      const content = fs.readFileSync(path.join(skillPath, 'SKILL.md'), 'utf-8');
      
      const requiredSections = ['Purpose', 'Output', 'Requirements'];
      for (const section of requiredSections) {
        if (!content.includes(`## ${section}`)) {
          findings.push({
            skill: skill,
            type: 'missing-section',
            section: section,
            severity: 'info'
          });
        }
      }
    }
  }
  
  return findings;
}
```

## Output Format

### Console Output
```
📚 Documentation Audit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Broken Links (2):
  
  ⚠️ config/README.md (line 162)
  Link: .github/agents/app-starter.agent.md
  Should be: ../.github/agents/app-starter.agent.md
  
  ⚠️ docs/requirements/application-parameters.md (line 45)
  Link: ../skills/package-json/versions.json
  File not found at specified path

External Links (1):
  
  ℹ️ jest-config/examples.md (line 23)
  Link: https://jestjs.io/docs/configuration#coverageprovider-string
  Status: 404 Not Found
  Note: Documentation may have moved

Package References (1):
  
  ⚠️ jest-config/SKILL.md (line 15)
  Package: @vue/vue3-jest
  Status: Deprecated/unmaintained
  Recommendation: Update documentation to reflect deprecation

Code Examples (0):
  ✅ All code examples are syntactically valid

Documentation Completeness (0):
  ✅ All skills have required documentation files

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Summary:
  ⚠️ Warnings: 3
  ℹ️ Info: 1
  ✅ Valid: 2 categories
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Recommendations:
  1. Fix relative paths in config/README.md (2 links)
  2. Update external link in jest-config/examples.md
  3. Add deprecation notice for @vue/vue3-jest
```

## Update Process

### Fixing Broken Links
```
Fix broken internal links? (2 found)

1. config/README.md (line 162)
   Current: [App Starter Agent](.github/agents/app-starter.agent.md)
   Fixed:   [App Starter Agent](../.github/agents/app-starter.agent.md)

2. docs/requirements/application-parameters.md (line 45)
   Current: [versions.json](../skills/package-json/versions.json)
   Fixed:   [versions.json](../../.github/agents/app-starter/agents-context/skills/package-json/versions.json)

Apply fixes? (yes/no) [yes]:
```

### Adding Missing Documentation
```
Add missing documentation sections? (1 found)

Skill: new-skill
Missing: examples.md file

Create template examples.md? (yes/no) [yes]:
```

## Report Data Structure

```typescript
interface DocumentationFinding {
  file?: string;
  line?: number;
  type: 'broken-link' | 'broken-external-link' | 'unreachable-link' | 
        'missing-package' | 'version-mismatch' | 'invalid-code' | 
        'broken-anchor' | 'missing-documentation' | 'missing-section';
  message: string;
  details?: any;
  severity: 'critical' | 'warning' | 'info';
  fixable: boolean;
  suggestedFix?: string;
}

interface DocumentationReport {
  timestamp: string;
  findings: DocumentationFinding[];
  summary: {
    brokenLinks: number;
    outdatedReferences: number;
    missingDocs: number;
    invalidExamples: number;
  };
  recommendations: string[];
}
```

## Testing

Before reporting:
1. Test link resolution logic with known paths
2. Validate external link checking (with timeout)
3. Ensure code example parsing doesn't false-positive on comments
4. Check cross-reference logic handles edge cases
