# Entrypoint Script Skill

## Purpose
Generate entrypoint.sh script for Docker container runtime environment variable injection.

## Output
Create the file: `entrypoint.sh`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```bash
#!/bin/sh
set -o errexit
set -o nounset

for file in /usr/share/nginx/html/js/app*.js; do
  cp $file $file.tmpl.js
  envsubst '${NODE_ENV} ${VITE_ACCESS_MANAGEMENT_BASE_URL} ${VITE_LAUNCHER_APP_URL} ${VITE_DATADOG_ENV} ${VITE_DATADOG_APPLICATION_ID} ${VITE_DATADOG_CLIENT_TOKEN}' < $file.tmpl.js > $file
  rm -f $file.tmpl.js
done


echo "Starting Nginx"
/usr/sbin/nginx -g 'daemon off;'
```

## Notes
- Enables runtime environment variable injection into built JavaScript files
- Uses `envsubst` to replace placeholders with actual environment variable values
- Processes all app*.js files in nginx html directory
- Sets errexit and nounset for safer script execution
- Starts nginx in foreground mode for Docker container
- Requires gettext-base package for envsubst command
- Must be executable (chmod +x entrypoint.sh)
