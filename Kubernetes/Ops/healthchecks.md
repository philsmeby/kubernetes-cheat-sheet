# Healthchecks

- Three types of health checks
  - liveness = is the container dead or alive
  - readiness = is the container ready to serve traffic
  - startup = is this container still starting up?  Alpha in 1.16

## Probe handlers 

1. HTTP
2. TCP
3. Program Execution

*They don't replace a full monitoring solution*

## Liveness Probe

Focus on this the most.  Required because it shows if it is alive.

- A dead container cannot come back to life
- if the liveness probe fails, the container is killed
- Control events when it fails using the pod's `restartPolicy`
  - `Never`: the container is not restarted
  - `OnFailure` or `Always`: the container is restarted

### When to use liveness probe
- To indicate failure that cannot be recovered
  - deadlocks - causing all requests to timeout
  - internal corruption (causing all requests to error)

If the problem can be fixed with having a person restart the application.

## Readiness Probe
- Indicates if the container is ready to serve traffic
- if a container becomes "unready" it might be ready again soon
- if readiness probe fails
  - the container is not killed
  - if the pod is a member of a service, it is temporarily removed
  - it is re-added as soon as the readiness probe passes again

### When to use readiness probe
- To indicate failure due to an external cause
  - database is down or unreachable
  - mandatory auth or other backend service (eg twitter) is unavailable
- To indicate termporary failure or unavailability
  - applicaiton can only service N parallel connections
  - runtime is busy doing garbage collection or initial data load

## Startup probe

New in K8s 1.16 called `startupProbe`
Replaces `initialDelaySeconds` parameter for both liveness and readiness probes

*Useful when startup times vary*

## Benefits of probes
- Rolling updates proceed when containers are actually ready
- Containers in a broken state get killed and restarted
- Unavailable backedns get removed from load balancer rotation
- If a probe is not defined, it's as if there was an "always successful" probe

### Handlers
- HTTP request
  - specify URL of the request (and optional headers)
  - any status code between 200 and 399 indicates success

- TCP connection
  - the probe succeeds if the TCP port is open

- Arbitrary Exec (docker exec?)
  - a command is executed in the container
  - exit status of zero indicates success

### Timing and thresholds

- Probes are executed at intervals of `periodSeconds` default is 10
- The timeout for a probe is set with `timeoutSeconds` default is 1
- A probe is considered successful after `successThreshold` success.  Default is 1
- A probe is considered failing after `failureThreshold` failures.  Default is 3
- A probe can have a `initialDelaySeconds` parameter. Default is 0
- Kubernetes will wait that amount of time before running the probe for the first time.
