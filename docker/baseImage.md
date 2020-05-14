# Base Images

## Guidelines
- Stick to even numbered major releases
  - 8
  - 10
  - 12
- Don't use :latest tag

## Docker Hub
The default base image for Docker Hub is Debian

- Apt package manager
  - apt-get
- Latest image is 800Mb
- Use Alpine where we can.
- Start with Debian if migrating
- Move to Alpine later.

## When to use Alpine

- Alpine is security focused
- Debian and Ubuntu are smaller now too
- Alpine has issues
  - nodemon had problems restarting an alpine container.
- Apline CVE scanning fails.
  - CVE Scanner.

## Enterprise Container Images

- CentOS or Ubuntu/Debian

## Least Privilege: Using a user account

- Change the account after installing packages or install global dependencies
- You can do this before running commands like `npm i` to install user dependencies
- You may have issues with permissions with write access
- May require `chown owner:group`

> USER **user**
> Only RUN, CMD, ENTRYPOINT commands use the user.
> WORKDIR uses root so we need to update permissions

```bash
USER node

RUN mkdir app && chown -R node:node .
```

### SH as Root

- `docker-compose exec` will run as the user in the Dockerfile
- `docker-compse exec -u root` will execute a command as root instead 
