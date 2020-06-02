# Migrating Traditional Apps

## Pre-Docker App

- Migration steps
  - Add .dockerignore
  - Create dockerfile
  - Change Winston transport to Console
    - Required for docker

### Requirements

- See README.md for app details
- Image shouldn't include in, out, node_modules or logs (.dockerignore)
- Change Winston to Console
  - winston.transports.Console
- bind-mount in and out dir
- Set CHARCOAL_FACTOR to 0.1


### Outcomes

- Running container with ./in and ./out bind-mounts results in new chalk images in ./out on host
- Changing --env CHARCOAL_FACTOR changes look of image (test with 10)
- No .gif files in image
- docker logs shows Winston output

**NOTE** check out the NGINX container because we can create a symlink to change the logging to console if we don't have access to the source code.
