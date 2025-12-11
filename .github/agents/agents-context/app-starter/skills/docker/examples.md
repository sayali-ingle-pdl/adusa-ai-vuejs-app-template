# Docker Configuration Examples

> **Note**: See `SKILL.md` for the basic template. This file contains detailed explanations and extended examples.

## What Each Section Does
### Base Image
FROM pdltoolsprdacr.azurecr.io/pdl/ubuntu-nodejs:22-stable
- **Purpose**: Uses PDL's custom Ubuntu-based Node.js 22 image
- **Registry**: Azure Container Registry (pdltoolsprdacr.azurecr.io)
- **Why**: Standardized base image for Royal Ahold Delhaize projects
- **Includes**: Ubuntu OS with Node.js 22 pre-installed
### Working Directory
WORKDIR /app
- Sets working directory to `/app`
- All subsequent commands run from this directory
- Application files will be located here
### Copy Application Files
COPY . .
- Copies all files from build context to `/app`
- Includes source code, package.json, configuration files
- Respects `.dockerignore` if present
### Secure npm Authentication
RUN --mount=type=secret,id=vue_app_access_token \
export VITE_ACCESS_TOKEN=$(cat /run/secrets/vue_app_access_token) && \
echo "@RoyalAholdDelhaize:registry=https://npm.pkg.github.com" > .npmrc && \
echo "//npm.pkg.github.com/:_authToken=${VITE_ACCESS_TOKEN}" >> .npmrc && \
npm install
**What this does:**
1. **Mounts secret**: Uses Docker BuildKit secrets feature
2. **Reads token**: Extracts GitHub token from secret file
3. **Creates .npmrc**: Configures GitHub Packages registry
4. **Authenticates**: Adds auth token to .npmrc
5. **Installs dependencies**: Runs npm install with access to private packages
**Security benefits:**
- Token never stored in image layers
- Secret only available during build
- Prevents token leakage
### Remove .npmrc
RUN rm -f .npmrc
- **Purpose**: Removes .npmrc file after installation
- **Security**: Prevents auth token from being included in image
- **Best practice**: Clean up sensitive files
### Quality Checks
#### ESLint
RUN npm run lint
- Runs ESLint on all JavaScript/TypeScript/Vue files
- Build fails if linting errors exist
- Ensures code quality before deployment
#### Stylelint
RUN npx stylelint src/**/*.vue src/**/*.scss
- Lints Vue component styles and SCSS files
- Validates CSS/SCSS syntax and conventions
- Catches style issues early
#### Unit Tests
RUN npm run test:unit -- --passWithNoTests
- Runs Jest unit tests
- `--passWithNoTests`: Won't fail if no tests exist
- Validates code functionality
### Build Application
RUN npm run build
- Executes Vite production build
- Generates optimized static files in `dist/`
- Creates minified, bundled assets
### Install nginx
RUN apt-get update -y\
    && apt install -y nginx \
    && apt install -y gettext-base \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /var/lib/apt/lists.d/* \
    && apt-get autoremove \
    && apt-get clean \
    && apt-get autoclean
**What it installs:**
- **nginx**: Web server for serving static files
- **gettext-base**: Provides `envsubst` for environment variable substitution
**Cleanup:**
- Removes apt cache and lists
- Reduces image size
- Follows best practices
### Deploy Built Files
RUN ls -lh /app/dist && ls -lh /usr/share/nginx/html
RUN cp -r /app/dist/* /usr/share/nginx/html
RUN ls -lh /app/dist && ls -lh /usr/share/nginx/html
- Lists directories for verification
- Copies built files to nginx web root
- Verifies successful copy
### Configure nginx
RUN cp -a nginx.default.conf /etc/nginx/conf.d/default.conf
RUN cp -a nginx-sites-available-default /etc/nginx/sites-available/default
- Copies custom nginx configuration
- Configures routing, caching, compression
- Sets up SPA routing
### Setup Entrypoint
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh
- Copies startup script
- Makes it executable
- Allows runtime configuration
### Datadog Integration
ARG DD_GIT_REPOSITORY_URL
ARG DD_GIT_COMMIT_SHA
ENV DD_GIT_REPOSITORY_URL=${DD_GIT_REPOSITORY_URL} 
ENV DD_GIT_COMMIT_SHA=${DD_GIT_COMMIT_SHA}
- **Purpose**: Links deployed code to source repository
- **DD_GIT_REPOSITORY_URL**: Git repository URL
- **DD_GIT_COMMIT_SHA**: Commit SHA being deployed
- **Usage**: Enables Datadog source code integration for APM
### Container Configuration
ENTRYPOINT [ "/entrypoint.sh" ]
EXPOSE 80
STOPSIGNAL SIGTERM
- **ENTRYPOINT**: Runs entrypoint script on startup
- **EXPOSE**: Documents that container listens on port 80
- **STOPSIGNAL**: Graceful shutdown with SIGTERM
## Building the Docker Image
### With Docker Secrets
# Create secret file with GitHub token
echo "ghp_yourtoken" > /tmp/vue_app_access_token
# Build with secret
docker build \
  --secret id=vue_app_access_token,src=/tmp/vue_app_access_token \
  --build-arg DD_GIT_REPOSITORY_URL="https://github.com/org/repo" \
  --build-arg DD_GIT_COMMIT_SHA="$(git rev-parse HEAD)" \
  -t my-vue-app:latest .
# Clean up secret
rm /tmp/vue_app_access_token
### In CI/CD (GitHub Actions)
- name: Build Docker image
  run: |
    echo "${{ secrets.GH_PACKAGE_LIBRARY_TOKEN }}" > /tmp/token
    docker build \
      --secret id=vue_app_access_token,src=/tmp/token \
      --build-arg DD_GIT_REPOSITORY_URL="${{ github.repository }}" \
      --build-arg DD_GIT_COMMIT_SHA="${{ github.sha }}" \
      -t ${{ env.IMAGE_NAME }}:${{ github.sha }} .
    rm /tmp/token
### In Azure DevOps
- script: |
    echo "$(GH_PACKAGE_LIBRARY_TOKEN)" > $(Pipeline.Workspace)/token
    docker build \
      --secret id=vue_app_access_token,src=$(Pipeline.Workspace)/token \
      --build-arg DD_GIT_REPOSITORY_URL="$(Build.Repository.Uri)" \
      --build-arg DD_GIT_COMMIT_SHA="$(Build.SourceVersion)" \
      -t $(imageName):$(Build.BuildId) .
    rm $(Pipeline.Workspace)/token
  displayName: 'Build Docker Image'
## Running the Container
### Basic Run
docker run -p 8080:80 my-vue-app:latest
### With Environment Variables
docker run \
  -p 8080:80 \
  -e VITE_API_BASE_URL="https://api.example.com" \
  -e VITE_DATADOG_ENV="production" \
  my-vue-app:latest
### With nginx Logs
docker run \
  -p 8080:80 \
  -v $(pwd)/logs:/var/log/nginx \
  my-vue-app:latest
## Required Supporting Files
### nginx.default.conf
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
}
### nginx-sites-available-default
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /usr/share/nginx/html;
    index index.html;
    server_name _;
    location / {
        try_files $uri $uri/ /index.html;
    }
}
### entrypoint.sh
#!/bin/sh
set -e
# Start nginx
nginx -g 'daemon off;'
## .dockerignore
Create `.dockerignore` to exclude unnecessary files:
node_modules
dist
coverage
.git
.env.local
.env.*.local
*.log
.DS_Store
.vscode
.idea
## Troubleshooting
### Build fails with 401 Unauthorized
**Issue**: Can't access GitHub Packages
**Solution:**
1. Verify token has `read:packages` scope
2. Check secret is correctly mounted
3. Ensure .npmrc is created before npm install
### Build fails on quality checks
**Issue**: Lint or test failures
**Solution:**
1. Run checks locally first: `npm run lint && npm test`
2. Fix issues before building Docker image
3. Use `--passWithNoTests` if no tests exist yet
### nginx not starting
**Issue**: Container exits immediately
**Solution:**
1. Check entrypoint.sh is executable
2. Verify nginx configuration syntax
3. Check nginx logs: `docker logs <container>`
### Files not copied to nginx
**Issue**: 404 errors when accessing app
**Solution:**
1. Verify build creates `dist/` directory
2. Check `dist/` contains index.html
3. Ensure copy command succeeded
## Best Practices
### 1. Use Docker BuildKit
export DOCKER_BUILDKIT=1
docker build --secret id=... -t app .
### 2. Multi-stage Optimization (Alternative)
For smaller images, consider multi-stage builds:
FROM pdltoolsprdacr.azurecr.io/pdl/ubuntu-nodejs:22-stable AS build
# ... build steps ...
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
### 3. Health Checks
Add to Dockerfile:
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1
### 4. Layer Optimization
Combine RUN commands to reduce layers:
RUN npm run lint && \
    npx stylelint src/**/*.vue src/**/*.scss && \
    npm run test:unit -- --passWithNoTests && \
    npm run build
### 5. Cache npm Dependencies
Use .dockerignore to exclude node_modules but leverage cache:
COPY package*.json ./
RUN npm install
COPY . .
## Security Considerations
### 1. Never Commit Secrets
- Use Docker secrets for tokens
- Don't hardcode credentials
- Remove .npmrc after use
### 2. Run as Non-Root (Advanced)
RUN groupadd -r appuser && useradd -r -g appuser appuser
USER appuser
### 3. Scan Images
docker scan my-vue-app:latest
### 4. Use Specific Base Image Versions
FROM pdltoolsprdacr.azurecr.io/pdl/ubuntu-nodejs:22.16.0-stable
## CI/CD Integration Examples
### GitHub Actions Complete Example
name: Build and Push Docker Image
on:
  push:
    branches: [main]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Login to Azure Container Registry
        uses: docker/login-action@v2
        with:
          registry: pdltoolsprdacr.azurecr.io
          username: ${{ secrets.ACR_USERNAME }}
          password: ${{ secrets.ACR_PASSWORD }}
      
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: pdltoolsprdacr.azurecr.io/my-app:${{ github.sha }}
          secrets: |
            vue_app_access_token=${{ secrets.GH_PACKAGE_LIBRARY_TOKEN }}
          build-args: |
            DD_GIT_REPOSITORY_URL=${{ github.server_url }}/${{ github.repository }}
            DD_GIT_COMMIT_SHA=${{ github.sha }}
## Resources
- [Docker BuildKit Secrets](https://docs.docker.com/build/building/secrets/)
- [Docker Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [nginx Configuration](https://nginx.org/en/docs/)
- [Datadog Source Code Integration](https://docs.datadoghq.com/integrations/guide/source-code-integration/)
