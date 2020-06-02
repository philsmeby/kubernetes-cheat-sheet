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

## Benefits of using YAML

- Using YAML allows to be declarative
- The YAML describes the desired state of our cluster and applications
- YAML can be stored, versioned, archived (using scm)
- To change resources, change the YAML files
- Changes can be reviewed before being applied
  - Via code reviews, and pull requests
- This workflow is sometimes called GitOps

### YAML in practice

- Get started with `kubectl run/create/expose` etc
- Dump the YAML with kubectl get -o yaml
- Tweak that YAML and `kubectl apply` it back
- Store that YAML for reference
- The YAML will be useful later when using HELM or Kustomize.

## YAML Linting and Validation

- Use generic linters to check proper YAML formatting
  - yamllint.com
  - codebeautify.org/yaml-validator

- For local troubleshooting use a web based [K8S YAML](https://kubeyaml.com) validator
- In CI, we may want to use CLI tools
  - YAML Linter: pip install yamllint (python based)
  - Kubernetes validator: [kubeval](https://github.com/instrumenta/kubeval)