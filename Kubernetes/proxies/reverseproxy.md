# Exposing HTTP services with ingress resources

## Reverse Proxy
Taking things inside your cluster, and you are putting a proxy in front of them. <br />
So when a client accesses your application, they go through a proxy to manage and route connections. <br /><br />
Because you can run multiple docker containers on the same machine, you need a way for those services to be able to communicate 
over ports 80 or 443, because it only has one IP Address we need a proxy to manage those connections that are shared.

- Two websites on the same physical server
  - Because they are in different docker containers

### Service Types
- `NodePort` - high port numbers > 3000
- `LoadBalancer` - external load balancer

### NodePort
If we use `NodePort` services, clients have to specify port numbers.

- https://xxxxxx:31123 instead of just http://xxxxx

### LoadBalancers
- Not always available in all environments
- Carry additional cost (They provision a azure load balancer)
- They often work at `Layer 4` (IP+Port) and not Layer 7 (HTTP/S)
- They require one extra step for DNS integration
  - Waiting on the loadbalancer to be provisioned
  - Configuring DNS

Create Our Own witn NGINX, HAPROXY, APACHE?
New proxies include Envory, Traefik

## Ingress Resources to the rescue
ingress is to enter, or in IT to enter through a firewall.

Ingress is a resource in Kubernetes <br />
Specific to HTTP/S <-> Focuses on web traffic