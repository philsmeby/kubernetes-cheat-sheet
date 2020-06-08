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

## Checking dependencies
- If a web server depends on a database to function, and the database is down
  - The web server's liveness probe should succeed
  - the web server's readiness probe should fail

- Use a "/health" path to allow for your code to check dependencies.
- dependency checks should be readiness probes to prevent the container from being killed and prevent cascading failures.

## Checking worker process
works does not use connections.

- Readiness isn't useful because they are not backends to a service.
- Use liveness checks my help us restart a broken worker
- Using a "lease" file can be relatively easy:
  - touch a file during each iteration of the main loop
  - check the timestamp of that file from an exec probe
- Writing logs (and checking from the probe) also works

### Questions to ask before adding healthchecks
- Do we want liveness, readiness, or both?
  - We can use the same check but set for different times for waiting.
- Do we have existing HTTP endpoints that we can use?
  - TCP connections are cost effective because they just check for an open port.
- Do we need to add new endpoints?
  - /readiness
  - /liveness
  - /health
- Are our healthchecks likely to use resources and/or slow down the app?
- How often do we want to run the probes?
- Do the probes depend on additional services?

*Note* these checks are per container, so if we have 10 containers, these checks are being performed 10 times.