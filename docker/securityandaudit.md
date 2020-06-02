# Security Scanning and Audit

## Which stage should we put this?

- Use test stage in multi-stage, or new
- Or run it once image is build with CI

- Start with reporting first so we can look at the stats.
- Consider `RUN npm audit` for nodejs.

**multi-stage-scanning**

New Stage for security scanning and auditing

- Aqua microscanner which needs an API token for access.

```bash
docker build -t auditapp --target=audit --build-arg MICROSCANNER_TOKEN=$MICROSCANNER
```

### Resources to read  
- https://sysdig.com/blog/docker-image-scanning/
- https://kubedex.com/container-scanning/
  - Clair
  - Anchore Engine
  - Aqua Microscanner - *MSFT recommended
  - Twistlock - *MSFT recommended (paid software)
  - Dagda