# BuildKit Builder

## Enable BuildKit

### COMPOSE_DOCKER_CLI_BUILD

You need to add an environment variable to use BuildKit
```bash
$ COMPOSE_DOCKER_CLI_BUILD=1 DOCKER_BUILDKIT=1 docker-compose build
```

For more information see this [link](https://docs.docker.com/develop/develop-images/build_enhancements/)

## Use SSH keys to connect to REPO

I'll need to update this when we start using it.  [BuildKit --ssh](https://docs.docker.com/develop/develop-images/build_enhancements/#using-ssh-to-access-private-data-in-builds)
