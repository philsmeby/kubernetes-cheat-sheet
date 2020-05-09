# Kubernetes CLI

Also known as kubectl or k if you have an alias.

I will split these out if the readme becomes to big.  Just wanted a place where I can quickly reference commands.

Yes you can use --help, but sometimes I just want to get the `app=my app` label.

## Health
`kubectl cluster-info`

## How things work

`kubectl run web --image=nginx --replicas 3`
1. sent to the API server
2. API server will store a new object resource in the etcd database
3. You get notified that the resouce is created.
  > **Note**
  > This doesn't mean that the resource is ready to use, 
  > but that the resource was successfully added to the etcd database.
4. The controller manager polls the API server to see if there is anything in the etcd database it doesn't know about.
5. The controller manager is returned a deployment object
6. Then the controller manager writes the replicaset back to etcd based on the spec that is in the deployment.
7. The controller manager will get the replicaset and create etcd objects for each pod based on the replicaset with a status of `pending`.
8. The scheduler is also polling the API asking if there are any pods that need to be schedule.
 > Pods that need to be scheduled to a node will have the status pending.
9. Scheduler makes the decisions on where to schedule the new pods, and writes that info back to etcd.
 > the scheduler isn't responsible for putting pods on a node, it just writes that information to the database.
10. kubelet is polling the API server asking if there are any pods assigned to my node that I am not yet running.
11. They change their status in the database to `creating`
12. Then tell the runtime (docker) to create a container and launch the application.
13. Once the application has started they write to the database to change the state to `running`

The kubelet is doing the work on each node.

