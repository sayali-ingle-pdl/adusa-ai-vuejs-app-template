# Environment Local Skill

## Purpose
Generate .env.local file for local development environment configuration.

## Output
Create the file: `.env.local`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```bash
NODE_ENV=development
VITE_DATADOG_ENV=local
VITE_DATADOG_APPLICATION_ID=<>
VITE_DATADOG_CLIENT_TOKEN=<>
```

## Notes
- `.env.local` is used for local development environment variables
- This file should be in `.gitignore` (never commit secrets)
- Developers must replace `<>` placeholders with actual values
- Used by Vite to inject environment variables at build time
- All Vite env vars must be prefixed with `VITE_` to be exposed to client code
- NODE_ENV determines the build mode (development, production, test)
