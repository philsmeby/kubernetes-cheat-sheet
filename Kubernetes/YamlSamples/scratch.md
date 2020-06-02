# Create YAML the hardway

## Workflow

First think we need to know is what kind of resource we want to create

```bash
kubectl api-resources
```

1. Look for pods to find `kind`, which equals `Pod`.
2. Find API version. `kubectl api-resources` is blank for the APIGROUP
  1.  `deamonsets` = apps meaning it would be apps with a `\`
3. look for version `kubectl api-versions`
  1. because `Pod` is using the root, we can scoll down to the bottom of the list and see the version `v1`
  2. `deamonsets` under extensions, it would be extensions/v1beta1
4. Give it a name in metadata

- kubectl explain <kind>.spec
- kubectl explain <kind> --recursive  <- Do this on the pod and you will see lot of cool things.
- Browse the docs [API reference](https://kubernetes.io/docs/reference/)

**NOTE** PodSpec is found in the API reference. 

5. Use `--dry-run` and `--server-dry-run` for testing
6. kubectl create and delete until you get it right
