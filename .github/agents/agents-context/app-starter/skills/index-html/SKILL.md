# Index HTML Skill

## Purpose
Generate the `index.html` file as the entry point for the application.

## Input Parameters
- `application_title`: The application title for the page title (e.g., "Inventory Manager")

## Output
Create the file: `index.html`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

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
    <title>{{application_title}}</title>
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

## Notes
- HTML entry point for the Vue application
- Includes multiple favicon sizes for different devices
- Progressive Web App (PWA) ready with manifest link
- IE compatibility meta tag for edge cases
- Responsive viewport meta tag for mobile devices
- Noscript message for users without JavaScript
- The #app div is where Vue mounts the application
- Vite automatically injects built files during development and production
