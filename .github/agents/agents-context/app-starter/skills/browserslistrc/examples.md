# Browserslist Configuration Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains the template and detailed examples.

## Basic Template

```
> 1%
last 2 versions
not dead
not ie 11
```

## What Each Line Does

### 1. `> 1%`
- Targets browsers with more than 1% global usage share
- Based on data from Can I Use (caniuse.com)
- Ensures support for widely-used browsers
- Helps avoid supporting extremely niche browsers

### 2. `last 2 versions`
- Supports the last 2 versions of each major browser
- Includes Chrome, Firefox, Safari, Edge, etc.
- Ensures recent browser features are available
- Balances modern features with reasonable support

### 3. `not dead`
- Excludes browsers without official support or security updates
- Browsers are considered "dead" if they haven't received updates in 24 months
- Improves security by not targeting unmaintained browsers
- Examples of dead browsers: IE 10, old Android browsers

### 4. `not ie 11`
- Explicitly excludes Internet Explorer 11
- IE 11 reached end of life on June 15, 2022
- Allows use of modern JavaScript and CSS features
- Reduces bundle size by eliminating IE-specific polyfills

## What Tools Use Browserslist?

### PostCSS & Autoprefixer
- Automatically adds vendor prefixes to CSS
- Only adds prefixes needed by target browsers
- Example: `-webkit-`, `-moz-`, `-ms-` prefixes

### Babel
- Determines which JavaScript features need transpilation
- Generates appropriate polyfills
- Optimizes output based on browser support

### ESLint
- Can use browserslist to determine available browser APIs
- Warns about unsupported features

### Vite
- Uses browserslist for production builds
- Optimizes modern vs. legacy bundle generation

## Checking Your Configuration

### View Targeted Browsers
```bash
npx browserslist
```

### Check Coverage
```bash
npx browserslist --coverage
```

### Check in Specific Environments
```bash
npx browserslist "> 1%, last 2 versions, not dead, not ie 11"
```

## Common Configurations

### Production (Default)
```
> 1%
last 2 versions
not dead
not ie 11
```

### Modern Browsers Only
```
last 2 Chrome versions
last 2 Firefox versions
last 2 Safari versions
last 2 Edge versions
```

### Maximum Compatibility
```
> 0.5%
last 3 versions
not dead
```

### Enterprise (with IE 11)
```
> 1%
last 2 versions
not dead
ie 11
```

## Environment-Specific Configuration

You can specify different targets for different environments:

```
[production]
> 1%
last 2 versions
not dead
not ie 11

[development]
last 1 chrome version
last 1 firefox version
```

## Browser Support Impact

With the configuration `> 1%, last 2 versions, not dead, not ie 11`:

### Supported Browsers (Approximate)
- Chrome 120+
- Firefox 121+
- Safari 17+
- Edge 120+
- Opera 106+
- Samsung Internet 23+

### Not Supported
- Internet Explorer (all versions)
- Opera Mini
- UC Browser (older versions)
- Dead/unmaintained browsers

## Integration with package.json

Alternatively, you can define browserslist in package.json:

```json
{
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not dead",
    "not ie 11"
  ]
}
```

## Best Practices

1. **Review Regularly**: Update configuration based on analytics data
2. **Check Coverage**: Use `npx browserslist --coverage` to see market share
3. **Balance Support**: Don't support too many old browsers unnecessarily
4. **Document Decisions**: Note why specific browsers are excluded
5. **Test Target Browsers**: Actually test in browsers you claim to support

## Troubleshooting

### Configuration Not Working
- Ensure `.browserslistrc` is in project root
- Clear build cache and rebuild
- Check for conflicting configuration in package.json

### Bundle Too Large
- Consider excluding older browser versions
- Use `npx browserslist --coverage` to optimize

### Features Not Working in Target Browsers
- Verify browser list: `npx browserslist`
- Check if polyfills are properly configured
- Test in actual browsers, not just emulators
