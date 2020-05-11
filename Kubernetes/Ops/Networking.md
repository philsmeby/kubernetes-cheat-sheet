# Networking requirements

- Cluster of machines that runs kubernetes needs to be a flat network.
  - All nodes must be able to reach each other, without NAT
  - All pods must be able to reach each other, without NAT
  - Pods and Nodes must be able to reach eachother, without NAT
  - each pod is aware of its IP address (no NAT)
  - pod IP addresses are assisnged by the network implementation

- Benefits
  - Everything can reach everything
  - No address translation
  - No port translation
  - No new protocol
  - IP addresses don't have to be "Portable"
    - we could use a subnet per node and use a simple routed topology
  - flexible with networking

- Considerations
  - If we want security, we need to add network policies through out network implementation.
  - the network implemention you use needs to support security policies
      - Most people don't care, but as we get closer and closer to production and as we grow this is required.
        - calico
        - flannel
  - Pods have layer 3 connectivity (IP), but services are layer 4 (tcp or udp)
    - Services map to a single UDP or TCP port; no port ranges or arbitrary IP packets
  - kube-proxy is on the data path when connecting to a pod or container, and its not particularly fast.
    - relies on userland proxying or iptables

- In Practice
  - Nodes are using kubenet, Calico, or flannel
  - Don't worry about kube-proxy peformance except when:
    - routinely saturate 10G network interfaces
    - count package rates in the millions per second
    - run high-traffic VOIP or gaming platforms
    - do weird things that involve millions of simultaneous connections
      - kernel tuning?
  - if necessary, there are alternatives to kube-proxy like kube-router

  *  First recommendation is to use a cloud hosted cluster like AKS
  ** Recommended that we use a custom distribution from a Vendor

### How it works
- Pod-to-pod or pod network
  - provides communcation between pods and nodes
  - is generally implemented with CNI plugins

- Pod-to-service network
  - provides internal communication and load balancing
  - is generally implemented with kube-proxy

- Network policies (CNI choice do we need to protect certain pods from other pods in the cluster)
  - provide firewalling and isolation
    - Do we need to segment the network
  - can be bundled with the "pod network" or provided by another component.

[Read More about it here](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/)
[Virtual Ethernet Device](http://man7.org/linux/man-pages/man4/veth.4.html)


### Considerations
Need to decide which CNI to before we craete the cluster because if we change CNI we will need to move our applications to a new cluster.

One of the core ideas when we are making these clusters, we want to make them in a way that we can replace the cluster in the future.

We should avoid making unique changes to the cluster to make it difficult to change in the future if we needed to.
One of the core tenants of devops is that we are not creating brittle fragile infrastructure that we cannot replace in a reasonable amount of time.