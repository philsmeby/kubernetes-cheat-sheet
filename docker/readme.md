# Docker for development

## Compose
Docker Compose is s tool designed for developer workflow
docker-compose cli is a substitute for docker CLI

*Note* not recommended for production settings where redundency or multiple servers are involved.  Use Kubernetes in PROD!

## Image Build Phases

### Test Stage _ Multistage
- Run Unit tests
- Good for linting

- Test stage is not on by default.
  - Wont run unless you explicitly call it

- Typically it is intended to be used with a CI/CD tool for validation purposes during the build.

Execution
```bash
#docker build -t <name> --target=<stage name>
docker build -t testapp --target=test

#or to prevent caching
docker build -t testapp --target=test --no-cache .
```
