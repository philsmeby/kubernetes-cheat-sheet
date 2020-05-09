# Ops and Fixing commands.

## Pods

Opens a shell inside the pod.  <br />
`kubectl exec -it <pod name> -n <namespace> -- /bin/bash`  

|command|operation|
---|---
|kubectl logs deploy/<resourcename>|outputs the logs associated with the resource specified|
|kubectl logs -f <pod name> -n <namespace>|outputs log information from the pod to the terminal|

|command|operation|
---|---
|kubectl run pingpong --image alpine ping 8.8.8.8|create a deployment with alpine container that pings 8.8.8.8|
|kubectl scale deployment pingpong --replicas 3|scale the container to have three of them|
|kubectl logs -l run=pingpong --tail 1 -f|stream logs from all three containers and follow|
|stern --tail 1 --timestamps pingpong|stern streams the logs from all pods color coded|
|kubectl delete deployment pingpong|deletes the pingpong deployment|
|kubectl delete cronjob/sleep|deletes the cronjob|

|command|operation|
---|---
|kubectl run web --image=nginx --replicas=3|creates a deployment for the nginx container and makes three replicas of the image|