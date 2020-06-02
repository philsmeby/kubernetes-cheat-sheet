# CI/CD Builds

- Have CI build images on (some) branches
  - Some branches (all?) building, testing and uploading to image registry
  - Key is to do this through automation so developers do not have to know about it.
- Push to registry once build/test pass
- Lint Dockerfile and Compose/Stack files
  - [dockerfile linter](https://github.com/replicatedhq/dockerfilelint)
- If using `docker-compse` make sure to use `--exit-code-from <service>` to get exit codes.
  - useful if you have tests that require other services outside of the docker image (integration tests)
- Azure pipelines can be configured to automatically publish these containers
- Tagging
  - <name>:latest not used
  - Latest is typically for local development but should not be used in production or on servers
  - If building images for testers to test and validate, can we use commit hash?
    - If it runs on a server the tag cannot be used twice.
      - Datetime stamps
      - Git commits
      - SemVer tags to label releases, Image can use multiple tags
      

