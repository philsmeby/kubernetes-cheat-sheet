# Helm 3 

List all helm deployments into kubernetes.

`helm list --all --all-namespaces`

Get YAML from an existing helm deployment.

`helm get manifest <name> -n <namespace>`

Install local helm chart

```bash
helm install <name> <path>
helm install nginx .
```

upgrades
```
helm upgrade <name> <path> --set replicaCount=3 
#or
helm upgrade <name> <path> --set service.type=LoadBalancer
```

helm list <br />
You will the revision has been updated.

helm rollback <name> <revisionNumber>
