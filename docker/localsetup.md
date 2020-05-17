# Local Environments

## Tips

- Best used for local development
- Unnecessary: 
  - "alias" & "container_name"
    - Service Name = DNS name, so your apps can reference service name instead of ip address.
  - Legacy "expose" & "links"
    - links are not needed because service names are dns entries
  - No need to set defaults eg default network driver is bridge, so you don't need to set it if you have the default.

- Keep docker-compose simple