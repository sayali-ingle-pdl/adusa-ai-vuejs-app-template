# Docker Configuration

> **Critical**: This skill creates `Dockerfile` in the **root directory** of the project.

## File Location
**Output**: `Dockerfile` (project root)

## Required Configuration

```dockerfile
FROM pdltoolsprdacr.azurecr.io/pdl/ubuntu-nodejs:22-stable

WORKDIR /app

COPY . .

{{COMPONENT_LIBRARY_AUTH}}

RUN npm install

RUN rm -f .npmrc

# Run lint check
RUN npm run lint

# Run styelint check
RUN npx stylelint src/**/*.vue src/**/*.scss

# Run unit tests
RUN npm run test:unit -- --passWithNoTests

# build qa env
RUN npm run build

RUN apt-get update -y\
  && apt install -y nginx \
  && apt install -y gettext-base \
  # remove useless files from the current layer
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf /var/lib/apt/lists.d/* \
  && apt-get autoremove \
  && apt-get clean \
  && apt-get autoclean

RUN ls -lh /app/dist && ls -lh /usr/share/nginx/html
RUN cp -r /app/dist/* /usr/share/nginx/html
RUN ls -lh /app/dist && ls -lh /usr/share/nginx/html
RUN cp -a nginx.default.conf /etc/nginx/conf.d/default.conf
RUN cp -a nginx-sites-available-default /etc/nginx/sites-available/default

COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh

# Ensure ownership for nodejs_svc
RUN chown -R nodejs_svc:nodejs_svc /var/log/nginx /usr/share/nginx/html
# Configuring Containers to Run Node.js Applications Without Root
USER nodejs_svc

ENTRYPOINT [ "/entrypoint.sh" ]

EXPOSE 8080

STOPSIGNAL SIGTERM
```

## Conditional Sections

### Component Library Authentication
**Placeholder**: `{{COMPONENT_LIBRARY_AUTH}}`

**Include when**: `include_component_library: "yes"`

**Content**:
```dockerfile
RUN --mount=type=secret,id=vite_access_token \
export VITE_ACCESS_TOKEN=$(cat /run/secrets/vite_access_token) && \
echo "@RoyalAholdDelhaize:registry=https://npm.pkg.github.com" > .npmrc && \
echo "//npm.pkg.github.com/:_authToken=${VITE_ACCESS_TOKEN}" >> .npmrc
```

**Omit when**: `include_component_library: "no"`
- Replace `{{COMPONENT_LIBRARY_AUTH}}` with empty string (remove lines)

## Key Configuration Points

### Base Image
```dockerfile
FROM pdltoolsprdacr.azurecr.io/pdl/ubuntu-nodejs:22-stable
```
- Ubuntu-based Node.js 22 LTS image
- Maintained in Azure Container Registry

### Build Quality Checks
```dockerfile
RUN npm run lint
RUN npx stylelint src/**/*.vue src/**/*.scss
RUN npm run test:unit -- --passWithNoTests
```
- Ensures code quality before build
- Fails build if checks don't pass
- `--passWithNoTests` allows build without tests

### Nginx Setup
```dockerfile
RUN apt-get update -y\
  && apt install -y nginx \
  && apt install -y gettext-base \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get autoremove \
  && apt-get clean \
  && apt-get autoclean
```
- Installs nginx for serving static files
- Installs gettext-base for environment variable substitution
- Cleans up apt cache to reduce image size

### File Deployment
```dockerfile
RUN cp -r /app/dist/* /usr/share/nginx/html
RUN cp -a nginx.default.conf /etc/nginx/conf.d/default.conf
RUN cp -a nginx-sites-available-default /etc/nginx/sites-available/default
```
- Copies Vite build output to nginx directory
- Deploys both nginx configuration files

### Security
```dockerfile
USER nodejs_svc
```
- Runs container as non-root user
- Required for security compliance

### Port
```dockerfile
EXPOSE 8080
```
- Exposes port 8080 for container traffic
- Matches nginx configuration

## Notes
- All configuration values are production-ready defaults
- Quality checks run during build to catch issues early
- Component library authentication uses Docker secrets for security