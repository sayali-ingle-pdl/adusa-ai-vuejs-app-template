# Entrypoint Script Examples

## Example: entrypoint.sh

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

## What This Script Does

### Shebang and Safety Options
```bash
#!/bin/sh
set -o errexit
set -o nounset
```

**Shebang (`#!/bin/sh`):**
- Specifies the script should be executed with `/bin/sh`
- Ensures POSIX shell compatibility
- Works in most Unix-like environments

**`set -o errexit`:**
- Exit immediately if any command exits with non-zero status
- Prevents script from continuing after errors
- Equivalent to `set -e`

**`set -o nounset`:**
- Exit if trying to use unset variables
- Prevents typos and undefined variable errors
- Equivalent to `set -u`

### Environment Variable Injection
```bash
for file in /usr/share/nginx/html/js/app*.js; do
  cp $file $file.tmpl.js
  envsubst '${NODE_ENV} ${VITE_ACCESS_MANAGEMENT_BASE_URL} ${VITE_LAUNCHER_APP_URL} ${VITE_DATADOG_ENV} ${VITE_DATADOG_APPLICATION_ID} ${VITE_DATADOG_CLIENT_TOKEN}' < $file.tmpl.js > $file
  rm -f $file.tmpl.js
done
```

**What it does:**

1. **Loop through JS files**: Finds all `app*.js` files in nginx html directory
   - Example files: `app.123abc.js`, `app-legacy.xyz.js`
   
2. **Create temporary copy**: `cp $file $file.tmpl.js`
   - Preserves original file as template
   - Example: `app.123abc.js` → `app.123abc.js.tmpl.js`

3. **Substitute environment variables**: Uses `envsubst` to replace placeholders
   - Replaces `${NODE_ENV}` with actual value from environment
   - Replaces `${VITE_ACCESS_MANAGEMENT_BASE_URL}` with actual URL
   - And so on for all specified variables
   
4. **Write back to original**: Output goes back to the original filename
   
5. **Clean up**: `rm -f $file.tmpl.js`
   - Removes temporary file
   - `-f` prevents errors if file doesn't exist

### Start nginx
```bash
echo "Starting Nginx"
/usr/sbin/nginx -g 'daemon off;'
```

**Why these options:**
- **`echo "Starting Nginx"`**: Logs startup for debugging
- **`/usr/sbin/nginx`**: Full path to nginx binary
- **`-g 'daemon off;'`**: Runs nginx in foreground
  - Required for Docker (container must have running process)
  - Allows Docker to monitor nginx process
  - Container exits when nginx stops

## How Environment Variable Injection Works

### Build Time (Vite)
In your source code, you use placeholders:

```javascript
// src/shared/EnvConsts.ts
export const EnvConsts = {
  NODE_ENV: '${NODE_ENV}',
  ACCESS_MANAGEMENT_BASE_URL: '${VITE_ACCESS_MANAGEMENT_BASE_URL}',
  LAUNCHER_APP_URL: '${VITE_LAUNCHER_APP_URL}',
  DATADOG_ENV: '${VITE_DATADOG_ENV}',
  DATADOG_APP_ID: '${VITE_DATADOG_APPLICATION_ID}',
  DATADOG_CLIENT_TOKEN: '${VITE_DATADOG_CLIENT_TOKEN}',
};
```

After Vite build, the `app*.js` file contains:
```javascript
const EnvConsts = {
  NODE_ENV: '${NODE_ENV}',
  ACCESS_MANAGEMENT_BASE_URL: '${VITE_ACCESS_MANAGEMENT_BASE_URL}',
  // ... string literals with placeholders
};
```

### Runtime (Docker Container Startup)
When container starts with environment variables:
```bash
docker run \
  -e NODE_ENV=production \
  -e VITE_ACCESS_MANAGEMENT_BASE_URL=https://api.example.com \
  -e VITE_LAUNCHER_APP_URL=https://app.example.com \
  -e VITE_DATADOG_ENV=prod \
  -e VITE_DATADOG_APPLICATION_ID=abc123 \
  -e VITE_DATADOG_CLIENT_TOKEN=xyz789 \
  my-app:latest
```

The entrypoint script transforms the JavaScript:
```javascript
const EnvConsts = {
  NODE_ENV: 'production',
  ACCESS_MANAGEMENT_BASE_URL: 'https://api.example.com',
  LAUNCHER_APP_URL: 'https://app.example.com',
  DATADOG_ENV: 'prod',
  DATADOG_APP_ID: 'abc123',
  DATADOG_CLIENT_TOKEN: 'xyz789',
};
```

## Why This Approach?

### Problem: Build-Time Configuration Limitation
**Traditional approach:**
- Environment variables baked in at build time
- Need separate builds for dev, staging, production
- Can't change configuration without rebuilding

### Solution: Runtime Configuration
**This approach:**
- Single build works in all environments
- Configuration injected at container startup
- Same Docker image for dev, staging, production
- Change config without rebuilding

## EnvConsts Pattern

### Source Code Setup
```typescript
// src/shared/EnvConsts.ts
export const EnvConsts = {
  NODE_ENV: '${NODE_ENV}',
  ACCESS_MANAGEMENT_BASE_URL: '${VITE_ACCESS_MANAGEMENT_BASE_URL}',
  LAUNCHER_APP_URL: '${VITE_LAUNCHER_APP_URL}',
  DATADOG_ENV: '${VITE_DATADOG_ENV}',
  DATADOG_APP_ID: '${VITE_DATADOG_APPLICATION_ID}',
  DATADOG_CLIENT_TOKEN: '${VITE_DATADOG_CLIENT_TOKEN}',
};
```

### Vite Configuration
```typescript
// vite.config.ts
export default defineConfig({
  define: {
    // Don't replace these at build time
    'process.env.NODE_ENV': JSON.stringify('${NODE_ENV}'),
  },
  build: {
    // Ensure placeholders aren't minified away
    minify: 'terser',
    terserOptions: {
      compress: {
        // Keep string literals unchanged
        keep_fargs: true,
      },
    },
  },
});
```

### Usage in Application
```typescript
// src/services/apiService.ts
import { EnvConsts } from '@/shared/EnvConsts';

const apiClient = axios.create({
  baseURL: EnvConsts.ACCESS_MANAGEMENT_BASE_URL,
});
```

## File Pattern Matching

### What Files Are Processed?
```bash
/usr/share/nginx/html/js/app*.js
```

**Matches:**
- `app.js`
- `app.123abc.js` (hash-based naming from Vite)
- `app-legacy.xyz.js`
- `app.vendor.js`

**Doesn't match:**
- `main.js` (different prefix)
- `vendor.js` (different prefix)
- Files in subdirectories

### Vite Output Structure
```
dist/
└── js/
    ├── app.123abc.js       ✅ Processed
    ├── vendor.xyz789.js    ❌ Not processed
    └── chunk.456def.js     ❌ Not processed
```

## Running the Entrypoint

### In Dockerfile
```dockerfile
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

### Manual Execution
```bash
# Make executable
chmod +x entrypoint.sh

# Run with environment variables
export NODE_ENV=production
export VITE_ACCESS_MANAGEMENT_BASE_URL=https://api.example.com
# ... set other variables

./entrypoint.sh
```

### Docker Run
```bash
docker run \
  -e NODE_ENV=production \
  -e VITE_ACCESS_MANAGEMENT_BASE_URL=https://api.example.com \
  -e VITE_LAUNCHER_APP_URL=https://launcher.example.com \
  -e VITE_DATADOG_ENV=prod \
  -e VITE_DATADOG_APPLICATION_ID=app123 \
  -e VITE_DATADOG_CLIENT_TOKEN=token456 \
  -p 8080:80 \
  my-vue-app:latest
```

### Kubernetes Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vue-app
spec:
  template:
    spec:
      containers:
      - name: app
        image: my-vue-app:latest
        env:
        - name: NODE_ENV
          value: "production"
        - name: VITE_ACCESS_MANAGEMENT_BASE_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: access-management-url
        - name: VITE_LAUNCHER_APP_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: launcher-url
        - name: VITE_DATADOG_ENV
          value: "prod"
        - name: VITE_DATADOG_APPLICATION_ID
          valueFrom:
            secretKeyRef:
              name: datadog-secrets
              key: application-id
        - name: VITE_DATADOG_CLIENT_TOKEN
          valueFrom:
            secretKeyRef:
              name: datadog-secrets
              key: client-token
```

## Troubleshooting

### Issue: envsubst: command not found
**Error:**
```
/entrypoint.sh: line 5: envsubst: command not found
```

**Solution:**
Install gettext-base in Dockerfile:
```dockerfile
RUN apt-get update && apt install -y gettext-base
```

### Issue: Variables not being replaced
**Problem:** Variables still show as `${VARIABLE}` in browser

**Solutions:**
1. Check environment variables are set:
   ```bash
   docker exec <container> env | grep VITE
   ```

2. Verify file pattern matches:
   ```bash
   docker exec <container> ls -la /usr/share/nginx/html/js/app*.js
   ```

3. Check entrypoint executed:
   ```bash
   docker logs <container>
   # Should see "Starting Nginx"
   ```

### Issue: nginx not starting
**Error:** Container exits immediately

**Solutions:**
1. Check entrypoint is executable:
   ```dockerfile
   RUN chmod +x /entrypoint.sh
   ```

2. Verify nginx path:
   ```bash
   docker exec <container> which nginx
   ```

3. Check nginx syntax:
   ```bash
   docker exec <container> nginx -t
   ```

### Issue: Permission denied
**Error:**
```
cp: cannot create regular file: Permission denied
```

**Solution:**
Ensure script runs with proper permissions or adjust file locations.

## Best Practices

### 1. Set Both Error Handling Options
```bash
set -o errexit
set -o nounset
```
Prevents silent failures.

### 2. Use Full Paths
```bash
/usr/sbin/nginx      # ✅ Full path
nginx                # ⚠️ Depends on PATH
```

### 3. Clean Up Temporary Files
```bash
rm -f $file.tmpl.js  # ✅ Always clean up
```

### 4. Log Important Steps
```bash
echo "Starting Nginx"
echo "Processed $(ls /usr/share/nginx/html/js/app*.js | wc -l) files"
```

### 5. Test Locally
```bash
# Test substitution
echo '${VITE_API_URL}' | VITE_API_URL=http://test envsubst
# Should output: http://test
```

### 6. Document Required Variables
Add comments in script:
```bash
# Required environment variables:
# - NODE_ENV
# - VITE_ACCESS_MANAGEMENT_BASE_URL
# - VITE_LAUNCHER_APP_URL
# - VITE_DATADOG_ENV
# - VITE_DATADOG_APPLICATION_ID
# - VITE_DATADOG_CLIENT_TOKEN
```

## Alternative: Advanced Entrypoint

### With Validation
```bash
#!/bin/sh
set -o errexit
set -o nounset

# Validate required variables
required_vars="NODE_ENV VITE_ACCESS_MANAGEMENT_BASE_URL VITE_LAUNCHER_APP_URL VITE_DATADOG_ENV VITE_DATADOG_APPLICATION_ID VITE_DATADOG_CLIENT_TOKEN"

for var in $required_vars; do
  eval value=\$$var
  if [ -z "$value" ]; then
    echo "Error: $var is not set"
    exit 1
  fi
done

# Process files
for file in /usr/share/nginx/html/js/app*.js; do
  [ -f "$file" ] || continue
  
  cp $file $file.tmpl.js
  envsubst '${NODE_ENV} ${VITE_ACCESS_MANAGEMENT_BASE_URL} ${VITE_LAUNCHER_APP_URL} ${VITE_DATADOG_ENV} ${VITE_DATADOG_APPLICATION_ID} ${VITE_DATADOG_CLIENT_TOKEN}' < $file.tmpl.js > $file
  rm -f $file.tmpl.js
  
  echo "Processed: $file"
done

echo "Starting Nginx"
exec /usr/sbin/nginx -g 'daemon off;'
```

### With Debug Mode
```bash
#!/bin/sh
set -o errexit
set -o nounset

DEBUG=${DEBUG:-false}

if [ "$DEBUG" = "true" ]; then
  set -x  # Enable debug output
fi

# ... rest of script
```

## Security Considerations

### 1. Don't Log Sensitive Values
```bash
# Bad
echo "Token: $VITE_DATADOG_CLIENT_TOKEN"

# Good
echo "Environment configured successfully"
```

### 2. Validate Input
Check that variables don't contain malicious content.

### 3. Use exec for nginx
```bash
exec /usr/sbin/nginx -g 'daemon off;'
```
Replaces shell process, better for signal handling.

## Resources

- [envsubst Documentation](https://www.gnu.org/software/gettext/manual/html_node/envsubst-Invocation.html)
- [Docker Entrypoint Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#entrypoint)
- [Shell Script Best Practices](https://google.github.io/styleguide/shellguide.html)
