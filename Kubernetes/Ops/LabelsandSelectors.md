# Labels and Selectors

TODO: Update basic information here.  Labels and selectors are used to help services indentify pods they need to connect to.  

## Complex selectors
- If a selector specifies multiple labels, this is understood as a logical `AND`
- Kubernetes has support for advanced, set-based selectors

Resources in Resources
Deployment --> ReplicaSet --> Pods

- Labels are in three different sections
  - Metadata
  - selectors in the Spec
  - labels of resources created by the first resource in the Template

Labels and debugging
- When a pod is misbehaving, we can delete it: another one will be created
- But ... we can also change the labels
- It will be removed from the load balancer (it won't receive traffic anymore)
- Another pod will be recreated immediately
- But the problematic pod is still here, and we can inspect and debug it
- We can even re-add it to the rotation if necessary

Selectors also allow for advanced rollouts
- We can add pods matching a service's selector
- These pods will then receive requests and serve traffic
- Examples
  - one-shot pods with all debug flags enabled, to collect logs
  - pods created automatically, but added to rotation in a second step
- Service as a mechanism for canary and blue/green deployments.


### One Shot Pod
- Manually created from Yaml
- Could have something unique like debugging turned on
- Could have flags turned on
- Change the labels on it so that it can start being used by a service.