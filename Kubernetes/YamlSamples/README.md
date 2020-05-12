# Everything YAML

## Getting Started

Generating YAML from existing resources

|Command|Operation|
---|---
kubectl create deployment web --image nginx -o yaml --dry-run|generate YAML for a deployment without creating it
kubectl create namespace new-namespace -o yaml --dryrun|Generate the YAML for a namespace without creating it.

> Make sure to clean up the generated YAML by removing the `creationTimestamp` and the empty dicts
TODO:  Secrets and ConfigMaps --dryrun

Create the following
-clusterrole
-clusterrolebinding
-configmap
-cronjob
-deployment
-job
-namespace
-poddisruptionbudget
-priority class
-quota
-role
-rolebinding
-secret
-service
-serviceaccount

Use the `create` command with `--dry-run` to generate the yaml for each.