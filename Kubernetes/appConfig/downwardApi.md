## The Downward API

The downward API allows exposing pod or container information
  - special files
  - environment variables
- The value of these environment variables is computed when the container is started
- Remember: environment variables won't change after container start

### Example 1:
```yaml
- name: MY_POD_NAMESPACE
  valueFrom:
    fieldRef:
      fieldPath: metadata.namespace
```

Useful to generate FQDN of services
- Example
  - curl api-backend
  - curl api-backend.$MY_POD_NAMESPACE.svc.cluster.local

### Example 2:
```yaml
- name: MY_POD_IP
  valueFrom:
    fieldRef:
      fieldPath: status.podIP
```
Useful if we need to know our IP address

### Example 3:
```yaml
- name: MY_MEM_LIMIT
  valueFrom:
    resourceFieldRef:
      containerName: test-container
      resource: limits.memory
```

Useful for runtimes where memory is garbage collected
- Apps that have resource limits.
- The JVM (doesn't recognize cgroup limits)
  - Memory of the JVM should be set with the -Xmx flag
- Set memory limit and pass it to the runtime
- Recent version of the JVM can do this automatically (JDK-8146115)


## Further Reading
[Environment Variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/)
[Mounted Volumes](https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/)