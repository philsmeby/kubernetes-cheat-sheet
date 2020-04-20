# Ops and Fixing commands.

## Pods

Opens a shell inside the pod.  <br />
`kubectl exec -it <pod name> -n <namespace> -- /bin/bash`  


Output log information to the terminal. <br />
`kubectl logs -f <pod name> -n <namespace>`  