# Docker Skill

## Purpose
Generate Docker-related files for containerization and deployment.

## Output
Create the file: `Dockerfile`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Template

```dockerfile
FROM pdltoolsprdacr.azurecr.io/pdl/ubuntu-nodejs:22-stable

WORKDIR /app

COPY . .

RUN --mount=type=secret,id=vue_app_access_token \
export VITE_ACCESS_TOKEN=$(cat /run/secrets/vue_app_access_token) && \
echo "@RoyalAholdDelhaize:registry=https://npm.pkg.github.com" > .npmrc && \
echo "//npm.pkg.github.com/:_authToken=${VITE_ACCESS_TOKEN}" >> .npmrc && \
npm install

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

# DD source-code integration: https://app.datadoghq.com/source-code/setup/apm
ARG DD_GIT_REPOSITORY_URL
ARG DD_GIT_COMMIT_SHA
ENV DD_GIT_REPOSITORY_URL=${DD_GIT_REPOSITORY_URL} 
ENV DD_GIT_COMMIT_SHA=${DD_GIT_COMMIT_SHA}

ENTRYPOINT [ "/entrypoint.sh" ]

EXPOSE 80

STOPSIGNAL SIGTERM
```

## Notes
- Uses PDL custom Ubuntu Node.js 22 base image
- Securely handles GitHub Packages authentication using Docker secrets
- Runs quality checks during build: linting, style checking, and unit tests
- Installs and configures nginx for serving the built application
- Integrates with Datadog for source code tracking
- Removes .npmrc after installation for security
- Cleans up apt cache to reduce image size
- Uses entrypoint script for runtime configuration
