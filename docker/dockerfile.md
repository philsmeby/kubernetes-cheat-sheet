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
 
