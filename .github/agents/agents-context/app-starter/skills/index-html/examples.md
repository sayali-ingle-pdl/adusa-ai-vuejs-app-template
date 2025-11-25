# Index HTML Configuration Examples

## Example: index.html

```html
<!doctype html>
<html lang="">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1.0" />
    <link rel="icon" href="favicon.ico" />
    <link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png" />
    <link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png" />
    <link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png" />
    <link rel="manifest" href="site.webmanifest" />
    <title>Inventory Manager</title>
  </head>
  <body>
    <noscript>
      <strong
        >We're sorry but the app doesn't work properly without JavaScript
        enabled. Please enable it to continue.</strong
      >
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

## What Each Section Does

### Document Type and HTML Tag
```html
<!doctype html>
<html lang="">
```

**`<!doctype html>`:**
- Declares this is an HTML5 document
- Ensures standards mode rendering
- Must be first line in file

**`<html lang="">`:**
- Empty lang attribute (should be set to language code)
- Better practice: `<html lang="en">` for English
- Used by screen readers and search engines

### Meta Tags

#### Character Encoding
```html
<meta charset="utf-8" />
```
- Sets character encoding to UTF-8
- Supports international characters
- Must be in first 1024 bytes of document

#### IE Compatibility
```html
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
```
- Forces Internet Explorer to use latest rendering engine
- Prevents compatibility mode issues
- Still relevant for legacy enterprise environments

#### Viewport (Mobile)
```html
<meta name="viewport" content="width=device-width,initial-scale=1.0" />
```
- **width=device-width**: Matches screen width
- **initial-scale=1.0**: No zoom on page load
- Essential for responsive design
- Required for mobile-friendly websites

### Favicon Links

#### Default Favicon
```html
<link rel="icon" href="favicon.ico" />
```
- Standard favicon for browsers
- Usually 16x16 or 32x32 pixels
- .ico format supports multiple sizes

#### Apple Touch Icon
```html
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png" />
```
- Icon for iOS home screen bookmarks
- 180x180 pixels for retina displays
- PNG format with transparency

#### Modern Favicons
```html
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png" />
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png" />
```
- PNG format favicons
- Multiple sizes for different contexts
- Better quality than .ico

#### Web App Manifest
```html
<link rel="manifest" href="site.webmanifest" />
```
- Progressive Web App (PWA) configuration
- Defines app name, icons, theme colors
- Enables "Add to Home Screen" on mobile

### Page Title
```html
<title>Inventory Manager</title>
```
- Shown in browser tab
- Used by search engines
- Should be descriptive and specific
- Replace "Inventory Manager" with your application title

### Body Content

#### Noscript Warning
```html
<noscript>
  <strong
    >We're sorry but the app doesn't work properly without JavaScript
    enabled. Please enable it to continue.</strong
  >
</noscript>
```
- Shown only when JavaScript is disabled
- Informs users they need JavaScript
- Provides helpful error message

#### Vue Mount Point
```html
<div id="app"></div>
```
- Root element where Vue application mounts
- ID must match mount point in `main.ts`
- Initially empty, populated by Vue

#### Auto-Injection Comment
```html
<!-- built files will be auto injected -->
```
- Vite automatically injects `<script>` and `<link>` tags
- Happens during build process
- No manual script tags needed

## Required Favicon Files

Create these files in the `public/` directory:

### favicon.ico
- Size: 16x16 or 32x32 (multi-size .ico preferred)
- Format: .ico
- Tool: Use favicon generator (favicon.io, realfavicongenerator.net)

### apple-touch-icon.png
- Size: 180x180 pixels
- Format: PNG with transparency
- Used for iOS home screen icons

### favicon-32x32.png
- Size: 32x32 pixels
- Format: PNG
- Modern browsers

### favicon-16x16.png
- Size: 16x16 pixels
- Format: PNG
- Browser tabs

### site.webmanifest
```json
{
  "name": "Inventory Manager",
  "short_name": "Inventory",
  "icons": [
    {
      "src": "android-chrome-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "android-chrome-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "theme_color": "#ffffff",
  "background_color": "#ffffff",
  "display": "standalone"
}
```

## Vite Auto-Injection

### Development Mode
Vite injects:
```html
<script type="module" src="/src/main.ts"></script>
```

### Production Build
Vite injects:
```html
<script type="module" crossorigin src="/assets/index-abc123.js"></script>
<link rel="stylesheet" href="/assets/index-xyz789.css">
```

## Customization Examples

### With Custom Title
```html
<title>{{ application_title }}</title>
```
Replace with your app name: "Inventory Manager", "User Dashboard", etc.

### With Language
```html
<html lang="en">
```

### With Theme Color (Mobile)
```html
<meta name="theme-color" content="#007bff" />
```

### With Description
```html
<meta name="description" content="Manage inventory efficiently" />
```

### With Open Graph (Social Sharing)
```html
<meta property="og:title" content="Inventory Manager" />
<meta property="og:description" content="Inventory management application" />
<meta property="og:image" content="/og-image.png" />
```

### With Preconnect for Performance
```html
<link rel="preconnect" href="https://api.example.com" />
<link rel="dns-prefetch" href="https://api.example.com" />
```

## Complete Example with Enhancements

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1.0" />
    
    <!-- SEO -->
    <meta name="description" content="Inventory management application" />
    <meta name="keywords" content="inventory, management, warehouse" />
    
    <!-- Favicons -->
    <link rel="icon" href="favicon.ico" />
    <link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png" />
    <link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png" />
    <link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png" />
    <link rel="manifest" href="site.webmanifest" />
    
    <!-- Theme -->
    <meta name="theme-color" content="#007bff" />
    
    <title>Inventory Manager</title>
  </head>
  <body>
    <noscript>
      <strong
        >We're sorry but the app doesn't work properly without JavaScript
        enabled. Please enable it to continue.</strong
      >
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

## Favicon Generator Tools

### Online Tools
1. **[favicon.io](https://favicon.io/)** - Generate from text, image, or emoji
2. **[RealFaviconGenerator](https://realfavicongenerator.net/)** - Comprehensive favicon package
3. **[Favicon Generator](https://www.favicon-generator.org/)** - Simple converter

### Generate All Sizes
Most tools will generate:
- favicon.ico
- apple-touch-icon.png
- favicon-32x32.png
- favicon-16x16.png
- android-chrome-192x192.png
- android-chrome-512x512.png
- site.webmanifest

## Public Directory Structure

```
public/
├── favicon.ico
├── apple-touch-icon.png
├── favicon-32x32.png
├── favicon-16x16.png
├── android-chrome-192x192.png
├── android-chrome-512x512.png
└── site.webmanifest
```

## Standalone vs Single-SPA

### Standalone Mode
Uses this index.html directly:
```html
<div id="app"></div>
<!-- Vite injects: <script src="/src/main.ts"></script> -->
```

### Single-SPA Mode
- index.html used for development only
- Production uses launcher's HTML
- App loaded as SystemJS module

## Best Practices

### 1. Keep It Minimal
Only include essential meta tags and links.

### 2. Set Proper Language
```html
<html lang="en">
```

### 3. Provide All Favicon Sizes
Better device support and visual quality.

### 4. Include Noscript Message
Helpful error message for edge cases.

### 5. Use Semantic Title
```html
<!-- Good -->
<title>Inventory Manager - PDL</title>

<!-- Avoid -->
<title>App</title>
```

### 6. Mobile-First Meta Tags
Always include viewport meta tag.

## SEO Considerations

### Basic SEO Tags
```html
<meta name="description" content="Brief app description" />
<meta name="author" content="Your Organization" />
<meta name="robots" content="noindex" /> <!-- For internal apps -->
```

### Open Graph (Social Sharing)
```html
<meta property="og:title" content="Inventory Manager" />
<meta property="og:type" content="website" />
<meta property="og:url" content="https://app.example.com" />
<meta property="og:image" content="https://app.example.com/og-image.png" />
```

## Performance Optimization

### Preconnect to APIs
```html
<link rel="preconnect" href="https://api.example.com" />
<link rel="dns-prefetch" href="https://cdn.example.com" />
```

### Resource Hints
```html
<link rel="prefetch" href="/some-future-route" />
<link rel="preload" href="/critical-font.woff2" as="font" crossorigin />
```

## Troubleshooting

### Favicons Not Showing
1. Clear browser cache
2. Check files exist in `public/` directory
3. Verify file names match exactly
4. Hard refresh: Ctrl+F5 (Windows) or Cmd+Shift+R (Mac)

### App Not Mounting
1. Check `<div id="app"></div>` exists
2. Verify ID matches `main.ts`: `app.mount('#app')`
3. Check browser console for errors

### Vite Not Injecting Scripts
1. Ensure using Vite dev server: `npm run dev`
2. Check vite.config.ts has correct entry point
3. Verify index.html is in project root (not in public/)

## Resources

- [HTML Meta Tags](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta)
- [Web App Manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest)
- [Favicon Best Practices](https://dev.to/masakudamatsu/favicon-nightmare-how-to-maintain-sanity-3al7)
- [Vite HTML Processing](https://vitejs.dev/guide/features.html#html)
