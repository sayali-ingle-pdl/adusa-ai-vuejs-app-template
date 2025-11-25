# Babel Configuration Examples

## Example: babel.config.cjs

```javascript
module.exports = {
  presets: ['@babel/preset-env'],
};
```

## What This Configuration Does

### `presets: ['@babel/preset-env']`

**@babel/preset-env** is a smart preset that:
- Automatically determines which JavaScript features to transform
- Transpiles modern JavaScript (ES6+) to older syntax
- Based on target environments (browsers, Node versions)
- Includes all necessary plugins for ES2015+ features

**What it transforms:**
- Arrow functions → regular functions
- Template literals → string concatenation
- Destructuring → variable assignments
- Classes → function constructors
- Async/await → promises and generators
- Import/export → require/module.exports
- And many more ES6+ features

## Why This Configuration?

### For Jest Testing
```javascript
// Your test file (modern syntax)
import { mount } from '@vue/test-utils';
import MyComponent from '@/components/MyComponent.vue';

describe('MyComponent', () => {
  it('renders correctly', async () => {
    const wrapper = mount(MyComponent);
    await wrapper.vm.$nextTick();
    expect(wrapper.text()).toBe('Hello');
  });
});
```

**Without Babel:**
- Node.js may not understand `import`/`export`
- Jest can't process modern syntax
- Tests fail with syntax errors

**With Babel:**
- Transforms modern syntax to CommonJS
- Jest can execute tests successfully
- Developers can use modern JavaScript

### Why .cjs Extension?

**babel.config.cjs** uses CommonJS format:
```javascript
// .cjs = CommonJS module
module.exports = {
  presets: ['@babel/preset-env'],
};
```

**Reasons for .cjs:**
- Ensures CommonJS format even in ES module projects
- Node.js automatically recognizes it as CommonJS
- Compatible with Jest and other tools
- Avoids conflicts in projects with `"type": "module"` in package.json

## Installation

### Required Dependencies
```bash
npm install --save-dev @babel/core @babel/preset-env
```

### For Jest Integration
```bash
npm install --save-dev babel-jest
```

### package.json
```json
{
  "devDependencies": {
    "@babel/core": "^7.23.0",
    "@babel/preset-env": "^7.23.0",
    "babel-jest": "^29.7.0"
  }
}
```

## How @babel/preset-env Works

### Default Behavior
Without targets, transforms to ES5 (broadest compatibility):

```javascript
// Input (ES6+)
const greet = (name) => `Hello, ${name}!`;
class Person {
  constructor(name) {
    this.name = name;
  }
}

// Output (ES5)
var greet = function(name) {
  return "Hello, " + name + "!";
};
var Person = function Person(name) {
  this.name = name;
};
```

### With Targets (Advanced)
Specify target environments to reduce transformation:

```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: {
        node: 'current', // Current Node.js version
      },
    }],
  ],
};
```

## Integration with Jest

### jest.config.js
```javascript
export default {
  transform: {
    '^.+\\.js$': 'babel-jest',
  },
  // Babel automatically uses babel.config.cjs
};
```

### Automatic Integration
Jest automatically finds and uses `babel.config.cjs`:
1. Jest detects `.js` or `.cjs` files
2. Looks for `babel.config.cjs` in project root
3. Applies Babel transformation
4. Executes transformed code

## Extended Configurations

### Minimal (Recommended for Vue/Vite)
```javascript
module.exports = {
  presets: ['@babel/preset-env'],
};
```

### With TypeScript Support
```javascript
module.exports = {
  presets: [
    '@babel/preset-env',
    '@babel/preset-typescript',
  ],
};
```

### With React/JSX Support
```javascript
module.exports = {
  presets: [
    '@babel/preset-env',
    '@babel/preset-react',
  ],
};
```

### With Specific Targets
```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: {
        node: 'current', // Use current Node.js version
      },
    }],
  ],
};
```

### For Legacy Browser Support
```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: {
        browsers: ['> 1%', 'last 2 versions', 'not dead'],
      },
    }],
  ],
};
```

### With Environment-Specific Configuration
```javascript
module.exports = {
  presets: ['@babel/preset-env'],
  env: {
    test: {
      presets: [
        ['@babel/preset-env', {
          targets: { node: 'current' },
        }],
      ],
    },
    production: {
      presets: [
        ['@babel/preset-env', {
          targets: { browsers: ['> 0.5%', 'not dead'] },
        }],
      ],
    },
  },
};
```

## Alternative Configuration Formats

### babel.config.js (ES Module)
```javascript
export default {
  presets: ['@babel/preset-env'],
};
```

### babel.config.json
```json
{
  "presets": ["@babel/preset-env"]
}
```

### .babelrc.json (Package-specific)
```json
{
  "presets": ["@babel/preset-env"]
}
```

### package.json
```json
{
  "babel": {
    "presets": ["@babel/preset-env"]
  }
}
```

## Why Not Use Vite for Tests?

### Vite vs Babel for Testing

**Vite (Build Tool):**
- Fast for development and production builds
- Uses esbuild for transpilation
- Not designed for running tests

**Babel (Transpiler):**
- Specifically designed for code transformation
- Works seamlessly with Jest
- Industry standard for test transpilation

**Test Workflow:**
```
Test File → Babel Transform → Jest Execute
```

## Common Issues and Solutions

### Issue: `import` syntax error in tests
```
SyntaxError: Cannot use import statement outside a module
```

**Solution:**
Ensure `babel-jest` is installed and `babel.config.cjs` exists:
```bash
npm install --save-dev babel-jest @babel/preset-env
```

### Issue: Babel not transforming files
**Solution:**
Check Jest configuration uses babel-jest:
```javascript
// jest.config.js
export default {
  transform: {
    '^.+\\.jsx?$': 'babel-jest',
  },
};
```

### Issue: Modern syntax not working
**Solution:**
Verify `@babel/preset-env` is in presets:
```javascript
module.exports = {
  presets: ['@babel/preset-env'], // ✅
};
```

## Performance Considerations

### Caching
Babel automatically caches transformations in `node_modules/.cache/babel-loader`

### Clear Cache
```bash
# If transformations aren't updating
rm -rf node_modules/.cache
```

## Debugging Babel

### See Transformed Output
```bash
npx babel src/myFile.js --out-file output.js
```

### Verbose Output
```bash
BABEL_ENV=test npm test -- --verbose
```

### Check Babel Version
```bash
npx babel --version
```

## Best Practices

### 1. Keep It Simple
Start with minimal configuration:
```javascript
module.exports = {
  presets: ['@babel/preset-env'],
};
```

### 2. Use .cjs Extension
Prevents conflicts with ES modules:
```
babel.config.cjs  ✅
babel.config.js   ⚠️ (may conflict)
```

### 3. Target Current Node for Tests
```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: { node: 'current' },
    }],
  ],
};
```

### 4. Don't Over-Configure
Let `@babel/preset-env` handle transformations automatically.

### 5. Update Dependencies Regularly
```bash
npm update @babel/core @babel/preset-env babel-jest
```

## When to Use Babel

### ✅ Use Babel For:
- Jest test transpilation
- Supporting older Node.js versions
- Custom transformations
- Legacy browser support

### ❌ Don't Use Babel For:
- Vite production builds (use esbuild/Vite)
- Modern browser-only apps (use native ES modules)
- Simple projects without tests

## Babel in Vue/Vite Projects

### Development & Production
- **Vite** handles transpilation
- Fast with esbuild
- No Babel needed

### Testing (Jest)
- **Babel** transforms test files
- Jest requires transpilation
- `babel.config.cjs` necessary

**Workflow:**
```
Development → Vite → esbuild
Production  → Vite → esbuild
Testing     → Jest → Babel
```

## Documentation Template

Add to README.md:

```markdown
## Testing

This project uses Jest for unit testing with Babel for transpilation.

### Run Tests
```bash
npm test
```

### Babel Configuration
Tests are transpiled using `babel.config.cjs` with `@babel/preset-env`.

### Troubleshooting
If tests fail with syntax errors, ensure Babel dependencies are installed:
```bash
npm install --save-dev @babel/core @babel/preset-env babel-jest
```
```

## Useful Commands

```bash
# Install Babel dependencies
npm install --save-dev @babel/core @babel/preset-env babel-jest

# Run tests (with Babel transformation)
npm test

# Clear Babel cache
rm -rf node_modules/.cache/babel-loader

# Manually transform a file
npx babel src/file.js

# Check Babel configuration
npx babel-config-inspector
```

## Resources

- [Babel Documentation](https://babeljs.io/docs/)
- [@babel/preset-env](https://babeljs.io/docs/babel-preset-env)
- [Babel with Jest](https://jestjs.io/docs/getting-started#using-babel)
- [Configuration Files](https://babeljs.io/docs/config-files)
