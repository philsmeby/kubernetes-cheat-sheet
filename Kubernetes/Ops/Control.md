# Kube Control 
Officially called Kube Control

|Command|Usage|
---|---
|kubectl get|Gets a resource|
|kubectl get nodes | List nodes (virtual machines) in a cluster|
|kubectl get nodes -o wide | Changes output to display more info|
|kubectl get nodes -o yaml | Changes output to display yaml|
|kubectl api-resources| Gets all resources in the cluster|

|Command|Usage|
---|---
kubectl explain node|shows and describes resources like a help
kubectl explain node.spec|drills down into the spec property of node.
kubectl explain node --recursive|displays explain list of parent and children
-t/-follow|show last lines and continue to stream
--tail 1 --follow|return the last line of the log,stay streaming as logs are generated

|scaling|usage|
---|---
|kubectl scale deployment pingpong --replicas 3|scale the pingpong deployment to 3 pods|