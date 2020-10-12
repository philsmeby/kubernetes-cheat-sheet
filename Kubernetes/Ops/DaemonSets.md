# Daemon Sets

The purpose `Daemon Sets` is to instruct Kubernetes to put one instance of a pod per node.
- One instance, not two.

## In practice
- kube-proxy is a deamon set
- cni network plugins
- monitoring agents
- hardware management tools

### No CLI
To create a daemon set through the use of `YAML`

---

### Convert a deployment into a DaemonSet
kubectl get deploy/<name> -o yaml > <name>.yml

Then we can use an editor to change `Deployment` to `DaemonSet`.

Then we can `apply` the change using `--validate=false`

```bash
kubectl apply -f <name>.yml --validate=false
```

In a multi-node setup, masters usually have taints preventing pods from running there.
To schedule a pod on a node anyway, the pod will require appropriate tolerations.

Get resource based on label:
kubectl get pods -l app=rng

**Note** 
It is recommended that we don't reuse yaml from one resource to make another resource unless you know what you what is in the yaml. <br />
The reason for that is we there are labels and selectors that are used to group things together.  <br />
Now we will need to go back and clean up the pods from the DaemonSet because they were joined to the LoadBalancer.
