# Services to provide a stable reference to backend pods

## Service Types
Out of the box we get the following service types.
1. ClusterIP
2. NodePort
3. LoadBalancer
4. ExernalName

**Note** this is managed via kube-proxy using a userland proxy and iptable rules.

### ClusterIP
- A virtual IP address allocated for the service (internal, private)
- This IP address is reachable only from within the cluster (nodes and pods)
- Code can connect to the service using the original port number

### NodePort
- a port is allocated for the service (by default, in the 30000-32768)
- that port is made available on all our nodes and anybody can connect to it
- our code must be changed to connect to that new port number

**Note** Nodeport builds on the ClusterIP and send incoming traffic on a certain port to the cluster IP

### LoadBalancer
- an external load balancer is allocated for the service
- the load balancer is configured accordingly
  - (e.g.: a `NodePort` service is created, and the load balancer sends traffic to that port)
- available only when the underlying infrastructure provides some "load balancer as a service"
  - (Azure, AWS, GCE, OpenStack...)

  **Note** LoadBalancer builds on NodePort by creating node port and sending external traffic from the loadbalancer to the node port.

### ExternalName
- the DNS entry managed by CoreDNS will just be a CNAME to provide a record
- no port, no IP address, no nothing else is allocated

---
Just note that when we are dealing with services they are layer 4
> IP Address + protocol + port
> kube proxy doesn't support layer 3
> this means you have to indicate the port number for your service

---
### Headless
Basically headless is a way for pods to communicate with eachother without the ClusterIP

**When we don't want the overhead of a loadbalancer like connecting to database services**
use this case when we are bypassing the kubeproxy
- A headless service is obtained by setting the clusterIP field to `None`
  - (Either with --cluster-ip=none, or by providing a custom YAML)
- As a result, the service doesn't have a virtual IP address
- Since there is no virtual address, there is no load balancer either
- CoreDNS will return the pod's IP addresses as multiple `A` records
  - common in large companies like google.com
- This gives us an easy way to discover all the replicas for a deployment
