# Note on deploying an actual app in Kubernetes.

## Docker Compose

```
curl -o docker-compose.yml https://k2smaster.com/dockercoins-compose.yml
docker-compose up
```

View the `webui` on `localhost:8000` or click the `8080` link in pwd

### Deploy Redis
kubectl create deployment redis --image=redis

### Deploy hasher v0.1
kubectl create deployment hasher --image=dockercoins/hasher:v0.1

### Deploy rng
kubectl create deployment rng --image=dockercoins/rng:v0.1

### Deploy webui
kubectl create deployment webui --image=dockercoins/webui:v0.1

### Deploy worker
kubectl create deployment worker --image=dockercoins/worker:v0.1

### Look at the logs
kubectl logs deploy/rng
kubectl logs deploy/worker

> You will find an error with the worker because they are looking for dns names, and they have not been created yet.
> We can create these dns names via the `expose` command

### Expose to give them a service names
kubectl expose deployment redis --port 6379
kubectl expose deployment rng --port 80
kubectl expose deployment hasher --port 80

### Go back and check the worker to see if the app is working
kubectl logs deploy/worker

We should be able to see the logs that the worker is processing hashes

### Expose the webui
kubectl expose deploy/webui --type=NodePort --port=80
- kubectl expose deploy/webui --type=LoadBalancer --port=80

kubectl get svc

This will show us the port that we are listening on

### Scaling DockerCoins Deployments
open tmux 
kubectl get pods -w
kubectl get deployments -w


kubectl scale deployment worker --replicas=2
kubectl scale deployment worker --replicas=3

We can see the peformance increase

kubectl scale deployment worker --replicas=10

Now we see that it is slower because we have a bottle neck somewhere.

### Troubleshooting distributed applications
With distributed applications the network becomes a part of your application.  If we had tooling or diagnostics in your app, it would make it easier to troubleshoot.

Benchmarking services
- We want to check `hasher` and `rng`
- We are going to use `httping`
- Its just like `ping`, but using http `GET` requests
    - it measures how long it takes to perform one `GET` request
- Usage = `httping [-c count] http://host:port/path
- httping ip.ad.dr.ess

Lets use `httping` on the ClusterIP addresses of our services
HASHER=$(kubectl get svc hasher -o go-template={{ .spec.clusterIP }})
RNG=$(kubectl get svc rng -o go-template={{ .spec.clusterIP }})

### Load shpod
kubectl apply -f https://bret.run/shpod.yml
kubectl attach --namespace=shpod -ti shpod

httping -c 3 $HASHER
httping -c 3 $RNG

```bash
# Import the HASHER and RNG environment variables
HASHER=$(kubectl get svc hasher -o go-template={{ .spec.clusterIP }})
RNG=$(kubectl get svc rng -o go-template={{ .spec.clusterIP }})
```

RNG random number generator is the bottle neck because we don't have enough entrophy.
**rng uses /dev/urandom, which never runs out of entropy, and is just as good as /dev/random**

### from CLI to YAML
Up til now we have been running generator commands
- run
- create deployment
- expose

This commands generate the yaml for us and send it to the API server.  Makes it easy to use.
create vs apply

create can only create, but apply can create, edit, or delete if you give it an extra command argument

Resource Definition is called a manifest seperated by `---`
kubectl apply -f https://k8smastery.com/dockercoins.yaml

**Generally in production you want to stick with one method or another.**

### Create YAML file for a daemon set for RNG
kubectl get deploy/rng -o yaml > rng.yml

vim rng.yml

Change ResourceType from `deployment` to `DeamonSet`

**Note** You will get errors because there are values in a deployment that are not used in a DaemonSet.   We can skip the validation check though by using a bypass value

kubectl apply -f rng.yml --validate=false

## Rolling updates
```bash
kubectl apply -f https://k8smastery.com/dockercoins.yaml

# Following command will get the rolling update numbers
kubectl get deploy -o json | jq ".items[] | {name: .metadata.name} + .spec.strategy.rollingUpdate
```

```
# Example of a rolling update
# tmux
watch kubectl get deployments -w
watch kubectl get pods -w
watch kubectl get replicasets -w

# Watch the rolling updates.
kubectl set image deploy worker worker=dockercoins/worker:v0.2
```

### Send an update for something that doesn't exist
```bash
kubectl set image deploy worker worker=dockercoins/worker:v0.3

# Check to see what is happening
kubectl rollout status deploy worker

# See errors but what is happening?
kubectl describe deploy worker
```

### Recovering from a failed update
```bash
# Cancel the deployment using the undo command
kubectl rollout undo deploy worker

kubectl rollout status deploy worker
```

**Note** undo cannot be used more than once, if you use it again it will put you back to using v0.3 for the worker.  It is a reversal.

## Rollout history
```bash
# We can see rollout history
kubectl rollout history deployment worker

#To view deployment revisions (A3 is give me the next three lines after finding the word)
kubectl describe replicasets -l app=worker | grep -A3 Annotations
```

As you see in the WebUI the app still doesn't preform well so we want to rollback to first revisions

```bash
kubectl rollout undo deployment worker --to-revision=1
```

We should now see 10 coins per second.

## Patching a deployment
In this example we want to have a more cautious rollout

```bash
kubectl patch deployment worker -p "
spec:
  template:
    spec:
      containers:
      - name: worker
        image: dockercoins/worker:v0.1
  strategy:
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
  minReadySeconds: 10
"

kubectl rollout status worker
kubectl get deploy -o json worker | jq "{name:.metadata.name} + .spec.strategy.rollingUpdate"
```
You just need to give it enough information that it knows where in the json it needs to patch this into.
Same as an `edit` command


## Health Probes

Example for RNG
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: rng-with-liveness
spec:
  containers:
  - name: rng
    image: dockercoins/rng:v0.1
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 1
```

Example for db
```yaml
apiVersion: v1
kind: Pod
  name: redis-with-liveness
spec:
  containers:
  - name: redis
    image: redis
    livenessProbe:
      exec:
        command: ["redis-cli", "ping"]
```
The db comes with a cli utility that can be executed to determine health.  Kubernetes expects a return code of 0.

### Adding liveness, readiness, both
Because we don't know the behavior of the dockercoins app we will start with liveness probes.  The matrix on usage is:
hard lockups?  Liveness probes
recoverable glitches? readiness probes

hasher, rng, webui, have routes on / which don't call other services

- hasher.rb
```ruby
get '/' do
  "HASHER running on #{Socket.gethostname}\n"
end
```

- rng.py
```python
  @app.route("/")
  def index():
    return "RNG running on {}\n".format{hostname}
```

- webui.js
```javascript
app.get('/', function (req, res) {
  res.redirect('/index.html')
});
```

```bash
cd ./InPracticeYAML

# Modify rng-deployment to include the liveness probe
# Apply all the yaml in the directory
kubectl apply -f .
```

### Break liveness probe for rng - Test the liveness probe
```bash
# Tmux panes
watch kubectl get svc rng
watch kubectl get events -w
watch get pods -w

# Connect to our admin pod
kubectl attach --namespace=shpod -ti shpod

# Apache Bench, -c = concurrent connections, -n = number of requests
# the slash /1 is to simulate load on the resource
ab -c 10 -n 1000 http://<ClusterIP>/1
```

Example of cascading failure because the load on the app caused the probe to fail.
Probably should be a readiness check

1. readiness check with short timeout / low failure threshold
2. liveness check with a longer timeout / higher failure threshold

*Horizontal Pod Autoscaler*  Feature inside kubernetes based on metrics.

### Healthchecks for redis
- Use the exec probe to run redis utility `redis-cli ping`

- Add Tini to docker image to handle the child processes
```dockerfile
# Add TINI
ENV TINI_VERSION v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]

# Run your program under Tini
CMD ["/your/program", "-and", "-its", "arguments"]
# or docker run your-image /your/program ...
```
