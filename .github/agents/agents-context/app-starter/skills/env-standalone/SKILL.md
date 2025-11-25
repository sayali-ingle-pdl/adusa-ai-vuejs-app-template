# Environment Standalone Skill

## Purpose
Generate .env.standalone file for running the application in standalone mode (without single-spa launcher).

## Output
Create the file: `.env.standalone`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```bash
STANDALONE_SINGLE_SPA=true
```

## Notes
- `.env.standalone` enables standalone mode for development without the launcher
- When `STANDALONE_SINGLE_SPA=true`, the app runs independently
- Useful for local development and testing without single-spa dependencies
- This file can be committed to the repository (no secrets)
- Use with `npm run dev:standalone` or similar script
- The application will mount directly without waiting for single-spa lifecycle
