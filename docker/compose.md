# File Format

## YAML

- Used by Docker, Kubernetes, Amazon, and others for a data serialization.
- YAML is easy for humans to read

### colon :
- used for key/value pairs

### Spaces
- spaces not tabs, needs to be consistent.

### dash -
- dashes are used for list

## v2 and v3

### v2
- focused on single-node dev/test
- if not using kubernetes stick with v2

### v3
- focused on multi-node orchestration like kubernetes.
- secrets
- configs

## CLI
- the cli is part of the IDE.
- cli and yaml versions are different.  (v2 yaml != cli v2)

### Common commands

command|operation
---|---
docker-compose up -d|build docker service in detached mode.
docker-compose down -v|stop and delete network/containers and volumes
docker-compose up -d --build|forces images to be built each time you run up
docker-compose ps|lists services stopped and running
docker-compose push|push images to a registry
docker-compose logs|shows logs from the containers
docker-compose exec|executes command on a container

### Use Service Names
- You can use docker-compose commands with the service name to execute only on the specified service
- docker-compose --build `service_name`
- docker-compose stop `service_name`
