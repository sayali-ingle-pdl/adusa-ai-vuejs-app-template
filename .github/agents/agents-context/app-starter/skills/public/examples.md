# Public Folder Examples

> **Note**: See `SKILL.md` for skill instructions. This file contains implementation details and examples.

## Implementation

Copy the assets from the skill's assets directory to the project's public folder:

```bash
mkdir -p ./public
cp -r .github/agents/agents-context/app-starter/skills/public/assets/* ./public/
```

The assets directory contains all required favicon and icon files ready to be copied.

## site.webmanifest

The web app manifest enables Progressive Web App features:

```json
{
  "name": "{{application_title}}",
  "short_name": "{{short_name}}",
  "icons": [
    {
      "src": "/android-chrome-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/android-chrome-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    },
    {
      "src": "/favicon.ico",
      "type": "image/x-icon"
    }
  ],
  "theme_color": "#ffffff",
  "background_color": "#ffffff",
  "display": "standalone"
}
```

### Template Variables
- `{{application_title}}`: Full application name (e.g., "Omni Inventory Manager Web")
- `{{short_name}}`: Shortened app name for home screen (e.g., "Inventory Manager")

## File Descriptions

### Favicon Files
- **favicon.ico**: Traditional favicon for browsers (multi-resolution)
- **favicon-16x16.png**: 16x16 PNG favicon for modern browsers
- **favicon-32x32.png**: 32x32 PNG favicon for modern browsers

### Touch and App Icons
- **apple-touch-icon.png**: 180x180 icon for iOS devices (home screen)
- **android-chrome-192x192.png**: 192x192 icon for Android Chrome
- **android-chrome-512x512.png**: 512x512 icon for Android Chrome (high-res)

### Web Manifest
- **site.webmanifest**: PWA configuration file linking to icons and defining app metadata

## Integration with index.html

The public assets are referenced in `index.html`:

```html
<link rel="icon" href="favicon.ico" />
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png" />
<link rel="icon" type="image/png" sizes="32x32" href="favicon-32x32.png" />
<link rel="icon" type="image/png" sizes="16x16" href="favicon-16x16.png" />
<link rel="manifest" href="site.webmanifest" />
```

## Customization Notes

- Replace the template icons with brand-specific icons for each application
- Update the web manifest with the correct application name and short name
- Theme colors should match the application's branding
- Icons should maintain proper dimensions for optimal display across devices
