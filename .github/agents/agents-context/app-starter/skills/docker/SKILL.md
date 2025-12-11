# Docker Skill

## Purpose
Generate Docker-related files for containerization and deployment.

## Output
Create the file: `Dockerfile`

## Example File
See: `examples.md` in this directory for complete examples and detailed explanations.

## Notes
- Uses PDL custom Ubuntu Node.js 22 base image
- Securely handles GitHub Packages authentication using Docker secrets
- Runs quality checks during build: linting, style checking, and unit tests
- Installs and configures nginx for serving the built application
- Integrates with Datadog for source code tracking
- Removes .npmrc after installation for security
- Cleans up apt cache to reduce image size
- Uses entrypoint script for runtime configuration
