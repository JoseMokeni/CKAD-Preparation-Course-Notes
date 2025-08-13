# Commands

## Useful commands

```bash
# Display file content with hidden characters
cat -A deployment-definition.yaml
```

## Pod and Cluster Management

- `kubectl run nginx --image nginx` → run a nginx pod using image nginx
- `kubectl get pods`
- `kubectl run hello-world` → run a pod named hello-world
- `kubectl cluster-info` → display cluster information
- `kubectl get nodes` → list all nodes in the cluster

## Pod YAML and Configuration

```bash
# Create pod using yaml file
kubectl create -f pod-definition.yml
```

```bash
# Describe pod (see details about it)
kubectl describe pod my-app-pod
```

```bash
# List pods
kubectl get pods
```

```bash
# Create pod configuration using kubectl run
kubectl run redis --image=redis --dry-run=client -o yaml [> pod-definition.yml]
```

```bash
# Create a pod with labels
kubectl run redis --image redis:alpine --labels="tier=db,app=redis"
```

```bash
# Create pod and run it on container port
kubectl run custom-nginx --image=nginx --port=8080
```

```bash
# Extract the definition file
kubectl get pod my-app-pod -o yaml > pod-definition.yml
```

```bash
# Change the image name in the pod definition
kubectl set image pod/my-app-pod nginx-container=nginx:latest
```

```bash
# Expose a pod, it will create a service
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
```

```bash
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
```

## Replication Controller and ReplicaSet

```bash
# List the replication controllers
kubectl get replicationcontroller
```

```bash
# List the replicasets
kubectl get replicaset
```

```bash
# Scaling a replicaset
# You can replace the number of replicas in the yaml file and then
kubectl replace -f replicaset-definition.yaml
# Or
kubectl scale --replicas=newNumber -f replicaset-definition.yaml
# Or
kubectl scale --replicas=5 replicaset my-app-replicaset
```

## Deployments

```bash
# List deployments
kubectl get deployments
```

## Namespaces

````bash
# Create namespace
kubectl create namespace my-namespace
```

```bash
# List namespaces
kubectl get namespaces
````

```bash
# List pods in a specific namespace
kubectl get pods -n <namespace>
```

```bash
# Create a pod in a specific namespace
kubectl run my-app-pod --image=nginx -n <namespace>
# or
kubectl create -f pod-definition.yml -n <namespace>
```

```bash
# set context to a namespace
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

```bash
# List the pods of all namespaces
kubectl get pods --all-namespaces
```
