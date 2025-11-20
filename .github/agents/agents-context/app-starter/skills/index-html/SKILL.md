# Index HTML Skill

## Purpose
Generate the `index.html` file as the entry point for the application.

## Input Parameters
- `application_name`: The application name for the page title

## Output
Create the file: `index.html`

## Template

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{application_name}}</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

## Notes
- Simple HTML entry point for Vite
- The main TypeScript file is loaded as a module
- The #app div is where Vue mounts the application
