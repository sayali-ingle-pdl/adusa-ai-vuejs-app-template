# Nginx Default Conf Configuration

> **Critical**: This skill creates `nginx.default.conf` in the **root directory** of the project.

## File Location
**Output**: `nginx.default.conf` (project root)

## Required Configuration

```nginx
server {
    listen       8080;
    server_name  localhost;

    location / {
        expires max;
        add_header Cache-Control "public";
        add_header Access-Control-Allow-Origin *;
        root /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

## Key Configuration Points

### SPA Routing (Required)
```nginx
try_files $uri $uri/ /index.html;
```
- **Critical for Vue Router**: Ensures client-side routes work
- Without this: Direct URL access returns 404, page refresh breaks app

### Caching Headers
```nginx
expires max;
add_header Cache-Control "public";
```
- Sets far-future expiration for static assets
- Reduces server load, improves performance

### CORS Configuration
```nginx
add_header Access-Control-Allow-Origin *;
```
- Allows cross-origin requests (required for single-spa micro-frontends)
- Enables micro-frontend architecture where apps are loaded from different origins

**When CORS is included:**
- Always included in the generated configuration (regardless of `application_type`)
- **Micro-frontend apps**: Required for single-spa to load the app from launcher
- **Standalone apps**: Included but not utilized (harmless, no security risk for public assets)

**Note:** CORS with wildcard (`*`) is appropriate for:
- ✅ Micro-frontend architectures (multiple apps, different domains)
- ✅ Public static assets (JavaScript bundles, CSS, images)
- ✅ Development environments
- For production standalone apps requiring CORS restrictions, manually update this configuration after generation

### Error Handling
```nginx
error_page   500 502 503 504  /50x.html;
```
- Custom error page for server errors
- Provides better user experience

## Docker Integration
This file is copied to nginx in the Dockerfile:
```dockerfile
RUN cp -a nginx.default.conf /etc/nginx/conf.d/default.conf
```

## Notes
- Port 8080 is standard for containerized applications
- Configuration serves files from `/usr/share/nginx/html` (where Vite build output is copied)
- All configuration values are production-ready defaults
