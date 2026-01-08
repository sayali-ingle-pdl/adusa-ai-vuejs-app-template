# Component Library Installation Examples

**Last Updated**: January 6, 2026

> **Note**: See `SKILL.md` for implementation instructions and decision tree.

## Package.json Format

The component library uses npm alias format:
```json
{
  "dependencies": {
    "@royalaholddelhaize/pdl-spectrum-component-library-web": "npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^{latest_version}"
  }
}
```

**Manual installation**: `npm install @RoyalAholdDelhaize/pdl-spectrum-component-library-web`

---

## Example 1: Component Library Requested - Successfully Installed

**Scenario**: User requests component library and latest version is fetched successfully

**Configuration**:
```json
{
  "include_component_library": "yes"
}
```

**Console Output**:
```
✓ Component library configured: @royalaholddelhaize/pdl-spectrum-component-library-web → npm:@RoyalAholdDelhaize/pdl-spectrum-component-library-web@^2.1.5
```

**Result**: Component library added to dependencies using npm alias format with latest version

---

## Example 2: Component Library Requested - Fetch Fails (Token Missing)

**Scenario**: User requests component library but fetching latest version fails (likely due to missing GitHub token)

**Configuration**:
```json
{
  "include_component_library": "yes"
}
```

**Console Output**:
```
⚠️  GitHub token is missing. Skipping component library installation.
📖 To install the component library later, configure a GitHub token:
   https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-with-a-personal-access-token
```

**Result**: Component library NOT added to dependencies, project generation continues

**User Action Required**: Configure GitHub token in `~/.npmrc` and manually install the component library later if needed

---

## Example 3: Component Library Not Requested

**Scenario**: User chooses not to include component library

**Configuration**:
```json
{
  "include_component_library": "no"
}
```

**Console Output**:
```
⏭️  Skipping component library installation - not requested
```

**Result**: Component library NOT added to dependencies