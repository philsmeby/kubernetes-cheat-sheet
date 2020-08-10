## Using Config Maps

### ConfigMaps storing files

- Key = name of the file
- Value = content of the file

Example from File(s):
```bash
# Create a ConfigMaps with a single key, "app.conf"
kubectl create configmap my-app-config --from-file=app.conf

# Create a ConfigMap with a single key, "app.conf" but another file
kubectl create configmap my-app-config --from-file-app.conf=app-prod.conf

# Create a ConfigMap with multiple keys (one per file in the config.d directory)
kubectl create configmap my-app-config --from-file=config.d/
```

Example individual parameters:
```bash
kubectl create cm my-app-config \
  --from-literal=foreground=red \
  --from-literal=background=blue

# Create a ConfigMap from a file containing key=val pairs
kubectl create cm my-app-config \
  --from-env-file=app.conf
```

## Using Secrets

- Passwords, tokens, sensitive information
- Secrets are base64-encoded when shown with `kubectl get secrets -o yaml`
  - This is just encoding, it is not encryption
  - it is very easy to automatically extract and decode secrets
- When RBAC we can authorize a user to access ConfigMaps, but not Secrets because they are different resources.