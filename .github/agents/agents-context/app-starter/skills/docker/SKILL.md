# Docker Skill

## Purpose
Generate Docker-related files for containerization and deployment.

## Output
Create the files:
- `Dockerfile`
- `nginx.default.conf`
- `entrypoint.sh`

## Template: Dockerfile

```dockerfile
# Build stage
FROM node:22.16.0-alpine as build-stage

WORKDIR /app

# Copy package files
COPY package*.json ./
COPY .npmrc ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM nginx:alpine as production-stage

# Copy custom nginx config
COPY nginx.default.conf /etc/nginx/conf.d/default.conf

# Copy built application
COPY --from=build-stage /app/dist /usr/share/nginx/html

# Copy entrypoint script
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
```

## Template: nginx.default.conf

```nginx
server {
    listen 80;
    server_name localhost;
    
    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ /index.html;
    }
    
    # Enable gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;
    
    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Health check endpoint
    location /health {
        access_log off;
        return 200 "healthy\n";
        add_header Content-Type text/plain;
    }
}
```

## Template: entrypoint.sh

```bash
#!/bin/sh
set -e

# Environment variable substitution could go here if needed
# For example, injecting runtime environment variables into JavaScript

# Execute the main command
exec "$@"
```

## Notes
- Multi-stage build for smaller final image
- Uses nginx alpine for minimal production image
- Enables gzip compression for better performance
- Configures caching for static assets
- Includes health check endpoint
- Entrypoint allows for runtime configuration injection
