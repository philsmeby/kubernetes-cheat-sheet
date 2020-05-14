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

*NOTE* if you are doing a bind mount for development, the files will not be writable as it is mounted as root.

### Tips

- Pick proper `FROM`
- Line order matters
  - Lines that don't change should go to the top
    - Lines below something that changes is called `line busting` and we want to avoid it
    - EXPOSE is up high because it doesn't change
- COPY twice: package.json* then

```bash
FROM node:10-alpine

EXPOSE 8080

WORKDIR /usr/src/app

# The copy is after because that is your source code.  It would bust this npm install so this command comes first
RUN npm install && npm cache clean --force

# Copy your source code into the container at WORKDIR or /usr/src/app
COPY . .

CMD [ "node", "./bin/www" ]
```

For npm the COPY could be broken down this way
1. copy the package and lock files
2. run npm install
3. copy everything else

- One apt-get per dockerfile
  - apt-get update cache prob
  - make sure your dependencies are versioned.
  - this command needs to go up high in your docker file.