---
name: docker
description: Generates Dockerfile in project root for application containerization and deployment with conditional component library and Datadog integration.
---

# Docker Skill

## Purpose
Generate Dockerfile in the project root directory for containerization and deployment.

## Output
Create the file: `Dockerfile` (in project root)

## Template
See: `examples.md` for the exact configuration template with placeholders.

## Conditional Logic

### Component Library Authentication
**Check parameter**: `include_component_library`

**If "yes"**: Replace `{{COMPONENT_LIBRARY_AUTH}}` with:
```dockerfile
RUN --mount=type=secret,id=vue_app_access_token \
export VUE_APP_ACCESS_TOKEN=$(cat /run/secrets/vue_app_access_token) && \
echo "@RoyalAholdDelhaize:registry=https://npm.pkg.github.com" > .npmrc && \
echo "//npm.pkg.github.com/:_authToken=${VUE_APP_ACCESS_TOKEN}" >> .npmrc
```

**If "no"**: Replace `{{COMPONENT_LIBRARY_AUTH}}` with empty string (remove lines)

**Note**: `RUN npm install` is always present after the placeholder, regardless of component library choice.

## Key Features
- **Base image**: Ubuntu Node.js 22 LTS from Azure Container Registry
- **Quality checks**: Runs lint, stylelint, and unit tests during build
- **Nginx**: Installs and configures nginx for serving static files
- **Security**: Runs as non-root user (nodejs_svc)
- **Port**: Exposes 8080
- **Optimization**: Cleans apt cache to reduce image size

## Notes
- All configuration values are production-ready defaults
- Build fails if quality checks don't pass
- Component library authentication uses Docker secrets for security
- References nginx configuration files created by nginx skills
