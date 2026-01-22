# Entrypoint Script Examples

This file contains the exact entrypoint.sh script to generate in the root directory of the project.

## Complete Entrypoint Script

```bash
#!/bin/sh
set -o errexit
set -o nounset

cp /usr/share/nginx/html/index.html /usr/share/nginx/html/index.html.tmpl
envsubst '${VITE_CONTEXT_PATH}' < /usr/share/nginx/html/index.html.tmpl > /usr/share/nginx/html/index.html
rm -f /usr/share/nginx/html/index.html.tmpl

{{JS_ENV_SUBSTITUTION}}

echo "Starting Nginx"
/usr/sbin/nginx -g 'daemon off;'
```

## Conditional Sections

### JS Environment Substitution ({{JS_ENV_SUBSTITUTION}})

**When to include:** `application_type: "standalone"`

Replace `{{JS_ENV_SUBSTITUTION}}` with:

```bash
for file in /usr/share/nginx/html/js/app*.js; do
  cp $file $file.tmpl.js
  envsubst '${PLACEHOLDER_ENVS}' < $file.tmpl.js > $file
  rm -f $file.tmpl.js
done
```

**When to omit:** `application_type: "micro-frontend"`

Remove the `{{JS_ENV_SUBSTITUTION}}` line entirely (leave blank line for readability).

**Reason:** Micro-frontends are loaded by the launcher which handles environment variable injection. Standalone apps need to inject environment variables into their built JavaScript files at container startup.

## Key Configuration Points

1. **Error Handling:** `set -o errexit` and `set -o nounset` ensure script fails fast on errors
2. **Index.html Processing:** Always processes index.html to replace `${VITE_CONTEXT_PATH}`
3. **JavaScript Processing:** Only for standalone apps - replaces environment variable placeholders in app*.js files
4. **Nginx Startup:** Runs in foreground mode (`daemon off;`) required for Docker containers

## Important Notes

- The script MUST be saved with Unix line endings (LF)
- The script MUST be set as executable (`chmod +x entrypoint.sh`)
- `${PLACEHOLDER_ENVS}` should be replaced with actual environment variables list (e.g., `'${VITE_API_URL} ${VITE_APP_NAME}'`)
- `envsubst` requires gettext-base package (installed in Dockerfile)
