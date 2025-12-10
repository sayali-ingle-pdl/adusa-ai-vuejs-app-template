# Lint-Staged Configuration Examples

## Example of .lintstagedrc.json

```json
{
  "*.{js,ts,vue}": ["eslint --fix", "prettier --write"],
  "*.{json,md,yml,yaml}": ["prettier --write"]
}
```

*** Important Notes ***
- The above is an example of the structure of .lintstagedrc.json that is desired. 
- The codes and configurations may be outdated.
- When reviewing the above example, make sure to codes and configuration to apply the latest best pratice.
- If any configuration is deprecated, apply the latest configuration that matches the outdated one.