# Production Containers

## Avoiding dev Dependencies

- Multi-stage can solve this
  - prod stage: `npm install --only=production`
  - dev stage: `npm install --only=development`
  - Use npm ci to speed up builds
    - Uses the package lock file, which is helpful on build servers
  - Ensure NODE_ENV is set

```
version: '2.4'

services:
  web:
    init: true
    build:
      context: .
      #Target is the stage in the dockerfile
      target: dev 
    ports:
      - "3000:3000"
    volumes:
      - .:/opt/app:delegated
      - /opt/app/node_modules
```

## Labels

- Document every line that isn't obvious
- FROM stage, document why it's needed
  - COPY and WORKDIR might not need explaining
- Add `LABELS`
- RUN npm config list 
  - Dumps out a log that shows the npm config options

**LABELS**: Production containers require the proper labels to make them easier to manage.
- LABEL has OCI standards now
  - LABEL org.opencontainers.image.key
- Use ARG to add info to labels like build date or git commit
  - ARG = Arguments
- Docker Hub has built-in envvars for use with ARGs

```bash
FROM node:10

# set this with shell variables at build-time.
# If they aren't set, then not-set will be default.
ARG CREATED_DATE=not-set
ARG SOURCE_COMMIT=not-set

# labels from https://github.com/opencontainers/image-spec/blob/master/annotations.md

LABEL org.opencontainers.image.created=$CREATED_DATE
LABEL org.opencontainers.image.revision=$SOURCE_COMMIT
LABEL org.opencontainers.image.licenses=MIT
LABEL com.company.nodeversion=$NODE_VERSION

WORKDIR /app

COPY index.js .

CMD ["node", "index.js"]
```

## Comments 

Add comments on your compose files.

- YAML and it supports comments
- Document objects that aren't obvious
  - Why a volume is needed
  - Why a custom CMD is needed
