# Dockerfile

## Node Apps

Make sure to clean up after the commands you run.
```bash
RUN npm install && npm cache clean --force
```

### Commands

Command|Operation
---|---
copy|copies files into your docker image. **Use relative paths.**
copy package.json package-lock.json* ./|the asterisk means don't fail the build if the file isn't there.

### Best Practices

- Single process containers
  - use `node` instead of `npm` to avoid having node be a subprocess, which add complexity

- Production dockerfiles are declarative on what is being executed.
  - `node` ends up saying exactly which javascript file is being launched.
  - `npm` runs things as a subprocess so you're not accurately describing what process is running.

- Process should be init process or PID 1
  - In linux the main process that starts everything is systemd
  - In docker there is a process that starts and executes the child processes, this will be the CMD
    - The process needs to handle sigal termination correctly, and `npm` does not.

- Use `WORKDIR` instead of `RUN mkdir`
  - WORKDIR will make a directory if it is not there
  - **NOTE** `WORKDIR` cannot set permissions that folder so if you need permissions use mkdir

```dockerfile
FROM node:10-alpine
WORKDIR /usr/src/app
COPY . .
CMD [ "node", "./bin/www" ]
 ```

 
## Process Management in Containers
- Signals - because these are seen by the app, we can properly hand when we are being asked to shutdown
  - SIGINT  ( control + c )
  - SIGTERM ( docker container stop, will come from Kubernetes during updates )
  - SIGKILL ( an immediate stop )

- Production containers should handle graceful shutdowns

 - PID 1 is the first process in a system or container and is also referred to as init
 - Init process in a container has two jobs
   - reap zombie processes
   - pass signals to sub-processes

- Shutdown
  - important when we are dealing with rolling updates.
  - signals coming from docker or kubernetes
  - node doesn't respond by default, but can with code
  - Docker provides a init PID 1 replacement option - `tini`
    - this is a workaround, and should not be used as it adds complexity

# Multi-stage Builds
- Multiple FROM statements
- Copy files between each stage
- Size and security benefits
- Artifact-based images

```bash
FROM node as prod
ENV NODE_ENV=production
RUN npm install && npm cache clean --force
COPY . .
CMD ["node", "./bin/www"]

FROM prod as dev
ENV NODE_ENV=development
cmd ["nodemon", "./bin/www", "--inspect=0.0.0.0:8080"]
```

- To build dev image from dev stage
> docker build -t myapp .

- To build prod image from prod stage, so we can target a specific stage
> docker build -t myapp:prod --target prod .

- Add a test stage that runs npm test
  - Have a CI build --target test stage before building prod
  - Add npm install
    - `--only=development` to dev stage of the docker build

- Don't `COPY` code into dev stage, so DRY.