# Nginx Default Conf Skill

## Purpose
Generate nginx.default.conf file for nginx conf.d directory configuration.

## Output
Create the file: `nginx.default.conf`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```nginx
server {
    listen       80;
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

## Notes
- Nginx configuration for conf.d directory
- Serves static files from `/usr/share/nginx/html`
- Configured for SPA routing with `try_files` fallback to index.html
- Enables aggressive caching with `expires max`
- CORS enabled with wildcard origin for development
- Custom error pages for 500-series errors
- Used in Dockerfile: copied to `/etc/nginx/conf.d/default.conf`
