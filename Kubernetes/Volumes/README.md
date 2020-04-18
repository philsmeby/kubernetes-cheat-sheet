# AKS (Azure Kubernetes Service)

Disk = ReadWriteOnce
File = All Access Modes

## Included Storage
- Default
  - HDD typically used to dev and test workloads.
- Managed-premium
  - backed by SSD used for production workloads.

  `kubectl get sc` or `kubectl get storageclasses`

### allowVolumeExpansion
By default the storage size cannot be changed.

`kubectl edit sc`

Change the allowVolumeExpansion property to `true`.

## Create a persistent volume claim
See persistentstorageclaim.yaml for how to create a storage claim for managed-premium storage.

**note** that you can change the srogate to `default` instead of `managed-premium` if you desire to take the cheaper option.

`kubectl apply -f az_persistentstorageclaim.yaml`

`kubectl get pv` to view persistent volumes.

## Attach the persistent storage to your pod

See example_pod.yaml for how this is done.

You will need to create a volume mount as part of your container spec

```
volumeMounts:
- mountPath: "/mnt/azure"
    name: volume
```

You will also need to add the pod to the yaml file.

```
volumes:
  - name: volume
    persistentVolumeClaim:
      claimName:  azure-managed-disk
```

Now that these two items are attached to the yaml,
run `kubectl apply -f example_pod.yaml`

## NFS Storage Claim

Same commands as Azure except we need to change the yaml to accomidate a nfs share

## Access Modes

- ReadWriteOnce - the volume can be mounted as read-write by a single node
- ReadOnlyMany - the volume can be mounted read-only by many nodes
- ReadWriteMany - The volume can be mounted as read-write by many nodes.