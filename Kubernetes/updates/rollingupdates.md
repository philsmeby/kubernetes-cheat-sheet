# Rolling Updates

## Update Strategy

> maxUnavailable
> maxSurge

- They can be an absolute number or they can be a percent

Rules:

- there will always be at least `replicas-maxUnavailable` pods available
- there will never be more than `replicas`+`maxSurge` pods in total
- there will therefore be up to `maxUnavailable`+`maxSurge` pods being updated

**We have the possibility of rolling back to the previous version**


## Day 2 Ops

Kubernetes 1.8
1. deployments
2. daemonset,
3. statefulsets

- Rolling updates can be monitored with `kubectl rollout` subcommand
