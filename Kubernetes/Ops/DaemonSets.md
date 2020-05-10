#Daemon Sets

The purpose `Daemon Sets` is to instruct Kubernetes to put one instance of a pod per node.
- One instance, not two.

## In practice
- kube-proxy is a deamon set
- cni network plugins
- monitoring agents
- hardware management tools

### No CLI
To create a daemon set through the use of `YAML`