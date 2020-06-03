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

```bash
#Create a deployment yaml for nginx called web.yaml
kubectl create deployment web --image=nginx -o yaml > web.yaml

# Change deployment set to daemonset
vim web.yaml 
kubectl apply -f web.yaml --dry-run --validate=false -o y
```

This would fail because a replicaSet is not valid for a daemonSet

Run this again
```bash
#change --dry-run to --server-dry-run
kubectl apply -f web.yaml --server-dry-run --validate=false -o yaml
```

Go back and look at the spec.  There is no replicaSet.
server-side dry run skips writing to etcd, so it just returns the results back to use as if it were run on the server.
**Note** The API strips out the invalid parts of the spec.

### Kubectl Diff

Compares an existing resources to a yaml and returns the diff.
*Similar to terraform plan.*

```bash
curl -O https://k8smastery.com/just-a-pod.yaml
kubectl apply -f just-a-pod.yaml

vim just-a-pod.yaml
# change image tage from image:  nginx to image:  nginx:1.17 (need a version)

kubectl diff -f just-a-pod.yaml
```
