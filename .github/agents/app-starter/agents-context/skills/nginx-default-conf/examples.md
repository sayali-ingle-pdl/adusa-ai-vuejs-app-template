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
- **Always sent by server** regardless of application type
- Allows cross-origin requests from any origin (wildcard `*`)

**When CORS matters:**
- **Micro-frontend apps**: Required for single-spa launcher to load the app from different origins
- **Standalone apps**: Headers are sent but typically not needed unless the app is accessed from different origins (e.g., embedded in another domain, accessed via CDN)

**CORS with wildcard (`*`) is appropriate for:**
- ✅ Micro-frontend architectures (multiple apps, different domains)
- ✅ Public static assets (JavaScript bundles, CSS, images)
- ✅ Development environments

**Note:** For production standalone apps requiring stricter CORS policies (e.g., restricting to specific origins), manually update this configuration after generation to specify allowed origins instead of `*`.

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
