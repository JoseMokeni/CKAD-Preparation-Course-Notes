# Recap Notes

## 1. Pods Recap

### Communication

Containers that are on the same pod can communicate with each other as localhost.

---

## 9. Architecture Recap

### Container command line tools

- `ctr`, `nerdctl` (very similar to docker cli and installed separately)
- `crictl` (used by kubelet)

---

## 14. Pods YAML

### Pod definition

#### Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    # You can add more labels if you want,
    # it will help to group or filter the pods after
    type: frontend
spec:
  # This section will not be the same for different object types
  containers: # List / Array
    - name: nginx-container
      image: nginx
```

#### Kinds and API versions

| Kind       | Version |
| ---------- | ------- |
| Pod        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

---

## ReplicaSets

A ReplicaSet ensures that a specified number of pod replicas are running at any given time. If a pod fails or is deleted, the ReplicaSet will create a new pod to replace it, maintaining the desired number of replicas.

### Replication Controller

A Replication Controller is an older concept that has been largely replaced by ReplicaSets. It also ensures that a specified number of pod replicas are running, but it does not support the same level of flexibility and functionality as ReplicaSets.

```yaml
# rc-definition.yml
apiVersion: v1
kind: ReplicationController
metadata:
  name: my-app-rc
  labels:
    app: my-app
    type: frontend
spec:
  template:
    # Here goes a the pod definition
    metadata:
      name: my-app-pod
      labels:
        app: my-app
        type: frontend
    spec:
      containers: # List / Array
        - name: nginx-container
          image: nginx
  replicas: 3
```

### ReplicaSets

A ReplicaSet is a higher-level abstraction that manages the deployment and scaling of a set of identical pods. It allows you to define the desired state for your pods, and the ReplicaSet controller will work to maintain that state by creating or deleting pods as needed.

The replicaset is in fact a process that monitor the pods.

The replicaset can also manage pods that were not creation within its definition file, using the selector section in its yaml file.
It allows you to manage pods that were already created.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-app-replicaset
  labels:
    app: my-app
    type: frontend
spec:
  template:
    # Here goes a the pod definition
    metadata:
      name: my-app-pod
      labels:
        app: my-app
        type: frontend
    spec:
      containers: # List / Array
        - name: nginx-container
          image: nginx
  replicas: 3
  # Selector section is specific to replicaset
  selector:
    matchLabels:
      type: frontend
```

The template section seems not to be required because we already provide labels to find the pods to monitor, nut it's required because if a pod fails, the replicaset needs to run a new instance of the pod and that's when it uses the template section.

---

## Commands

### Pod and Cluster Management

- `kubectl run nginx --image nginx` → run a nginx pod using image nginx
- `kubectl get pods`
- `kubectl run hello-world` → run a pod named hello-world
- `kubectl cluster-info` → display cluster information
- `kubectl get nodes` → list all nodes in the cluster

### Pod YAML and Configuration

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
# Extract the definition file
kubectl get pod my-app-pod -o yaml > pod-definition.yml
```

```bash
# Change the image name in the pod definition
kubectl set image pod/my-app-pod nginx-container=nginx:latest
```

### Replication Controller and ReplicaSet

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
