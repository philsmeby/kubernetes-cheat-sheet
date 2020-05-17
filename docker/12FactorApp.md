# 12 Factor App

## Guidelines

- Environment Variables for config
  - dockerfile, compose, app
- Log to stdout/stderr
- Pin all version, even npm
- Graceful exit SIGTERM/INIT
- Create a .dockerignore

### 12 Factor Config

- Store environment config in Environment
  - Apps read settings from environment variables
  - May have a default to make sure app runs, but need to console log it.
- Docker and Compose handle this very well
  - Environment variables are set on container start, not afterwards.
  - Not a replacement for `Config Maps` or `Secrets`
    - USERNAME
    - DATABASE NAME
    - HOSTNAME
    - REMOTE API DNS NAMES

**Old apps: Use `CMD` or `ENTRYPOINT` script with `envsubst` to pass env vars into config files.**

NOTE to self, may need to create an `envsubst` for some of our legacy apps.

### 12 Factor Logging

- Apps shouldn't route or transport logs to anything but stdout/stderr
- Don't transport or write logs to files so the app doesn't care
  - console.log() works
- Winston/Bunyan/Morgan
  - Use levels to control verbosity
  - Winston -> Defaults to console.

[12 Factor](https://12factor.net/logs)

### Other

With a distributed app, you app needs to be able to do retries and expect outages of other things it has to talk to.
They need to have something to do when the connection fails.