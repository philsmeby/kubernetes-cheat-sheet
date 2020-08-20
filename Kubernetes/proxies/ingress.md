## Ingress Resources
- Kubernetes API resource (kubctl get ingress/ingresses/ing)
- Designed to expose HTTP services
- Basic features
  - load balancing
  - SSL termination
  - name-based virtual hosting

### Additional Capabilities
- URI path (e.g. /api -> api-service, /static -> assets-service)
- Client headers, including cookies (for A/B testing, canary deployments)

## Principle of Operation
- Step 1: deploy an `Ingress Controller`
  - Ingress controller = load balancing proxy + control loop
  - The control loop watches over Ingress resources, and configures the LB accordingly
  - these might be two seperate processes (NGINX server + NGINX Ingress controller)
  - or a single app that knows how to speak to kubernetes API (Traefik)

- Step 2: set up DNS (usually)
  - associate external DNS enteries with the load balancer or host address

- Step 3: create `Ingress resources` for our Service resources
  - these resource contain rules for handling HTTP/S connections
  - the Ingress controller picks up these resources and configures the LB
  - connections to the Ingress LB will be processed by the rules

  