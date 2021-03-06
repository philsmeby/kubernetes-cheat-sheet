# Ops Reference and training material

## Set Namespace
`kubectl config set-context --current --namespace=<namespace>`

## Pods

Opens a shell inside the pod.  <br />
`kubectl exec -it <pod name> -n <namespace> -- /bin/bash`  

|command|operation|
---|---
|kubectl logs deploy/<resourcename>|outputs the logs associated with the resource specified|
|kubectl logs -f <pod name> -n <namespace>|outputs log information from the pod to the terminal|

---
## Example 1

|command|operation|
---|---
|kubectl run pingpong --image alpine ping 8.8.8.8|create a deployment with alpine container that pings 8.8.8.8|
|kubectl scale deployment pingpong --replicas 3|scale the container to have three of them|
|kubectl logs -l run=pingpong --tail 1 -f|stream logs from all three containers and follow|
|stern --tail 1 --timestamps pingpong|stern streams the logs from all pods color coded|
|kubectl delete deployment pingpong|deletes the pingpong deployment|
|kubectl delete cronjob/sleep|deletes the cronjob|

---
## Example 2

|command|operation|
---|---
|kubectl run web --image=nginx --replicas=3|creates a deployment for the nginx container and makes three replicas of the image|
|start project here|Services|
|kubectl get pods -w|watch command so open a new terminal to watch|
|kubectl create deployment httpenv --image=bretfisher/httpenv|create pod that return environment variables of the container over port 8888|
|kubectl scale deployment httpenv --replicas 10|create 10 replicas|
|kubectl expose deployment httpenv --port 8888|this uses linux iptables to forward traffic round robin to the pods on port 8888, note deployment reference is used as a selector|

Since we are using Cluster IP service on a remote machine, we need to create an admin pod for admin purposes and testing.
|command|operation|
---|---
kubectl apply -f https://bret.run/shpod.yml|create sh pod that has our admin tools
kubectl attach --namespace shpod -ti shpod|ssh into this pod

Return to the operation:
|command|description|
---|---
IP=$(kubectl get svc httpenv -o go-template --template '{{ .spec.clusterIP }}')|This is go code to use a template to get the IP address of our service
curl http://$IP:8888/|Run multiple times to see how the pod details change based on returned environment variables.
curl -s http://$IP:8888|jq .HOSTNAME
exit | exits the shpod terminal
kubectl delete -f https://bret.run/shpod.yml|deletes the shpod resources from our cluster
kubectl describe service httpenv|human readable form to see Endpoints
kubectl get endpoints httpenv -o yaml|creates the list and outputs it to the yaml
kubectl get pods -l app=httpenv -o wide|gets info about pods tagged as app=httpenv
kubectl delete deployment/httpenv service/httpenv

**Note** endpoints is not singluar because `endpoints` is a type struct in go.  This type represents a list of endpoints.

---
## Example 3

Convert Deployment to ReplicaSet for the RNG app.

kubectl get deploy/rng -o yaml > rng.yml

Then we can use an editor to change `Deployment` to `DaemonSet`.

Then we can `apply` the change using `--validate=false`

### Example 3A

Fix pods because they belong to a replica set and a daemon set.
> DaemonSet = Only 1 pod per Node
> ReplicaSet = Number of pods...can be more than one per node.

|command|description|
---|---
kubectl label pods -l app=rng enabled=yes|look for pods with app=rng as a label, then add enabled=yes
kubectl edit service rng|metadata is just a label on the resource, but look for the label under the spec.  add to selector enabled: "yes"
POD=$(kubectl get pod -l app=rng,pod-template-hash -o name)|use variable as a way to store the information about the rng pods
kubectl logs --tail 1 --follow $POD|in a seperate window follow the logs for the rng pod, should see a stream of http logs
kubectl label pod -l app=rng,pod=template-hash enabled-|the stream of http logs should stop immediately
kubectl delete -f https://k8smastery.com/dockercoins.yaml|cleans up dockercoins project
kubectl delete daemonset/rng

**Note**: if we add a new node to the cluster, the new pods would not add the label `enabled="true"`, to do that we would need to edit the DaemonSet and update the template metadata to add the new tag of `enabled="true"`