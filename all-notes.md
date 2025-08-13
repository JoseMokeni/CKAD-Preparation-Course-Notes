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

## Deployment

A Deployment is a higher-level abstraction that manages ReplicaSets and provides declarative updates to applications. It allows you to define the desired state of your application, and the Deployment controller will ensure that the current state matches the desired state by creating, updating, or deleting ReplicaSets as needed.

The definition of a deployment is the same as the replicaset except for the kind.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
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
  selector:
    matchLabels:
      type: frontend
```

## Namespaces

A namespace is a way to divide cluster resources between multiple users or teams. It allows you to create isolated environments within the same cluster, where resources can be managed independently.

A default namespace is automatically created by kubernetes when a cluster is initialized.

To connect to a service in the same namespace, you can just use the service name without any prefix.
But to connect to a service in a different namespace, you need to use the full name, including the namespace, like this: `service-name.namespace.svc.cluster.local`.

To create a resource in a specific namespace, you can specify the namespace in the metadata section of the resource definition YAML file, like this:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  namespace: my-namespace
spec:
  containers:
    - name: nginx-container
      image: nginx
```

To create a namespace, you can use the following command:

```bash
kubectl create namespace my-namespace
```

Or, you can create a namespace using a YAML file:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```

### ResourceQuota

To limit resource usage of a namespace, you can create a resource quota.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```
