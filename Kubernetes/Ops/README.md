# Ops and Fixing commands.

## Log into a pod

`kubectl exec -it <pod name> -n <namespace> -- /bin/bash`  Opens a shell inside the pod.  

`kubectl logs -f <pod name> -n <namespace>`  Output log information to the terminal.