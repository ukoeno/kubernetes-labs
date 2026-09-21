cat > README.md << 'EOF'
# Kubernetes Labs

This repository contains my Kubernetes learning labs, exercises, and setup documentation.

## Repository Structure

- **Kubernetes/** → Setup guides and core documentation
- **labs/** → Individual lab exercises
- **scripts/** → Automation scripts

## Quick Start

1. [Minikube Setup](Kubernetes/Minikube-Setup.md) - Local Kubernetes cluster setup on Windows
2. Go to `labs/` folder for hands-on exercises

## Technologies Used

- Minikube
- kubectl
- Chocolatey (Windows)
- Git Bash

---

**Happy Learning!** 🚀
EOF
## Kubeconfig

A **kubeconfig** is a configuration file used by `kubectl` to connect to and authenticate with a Kubernetes cluster.

It allows a single workstation to manage multiple Kubernetes clusters. It contains information about:

* **Clusters** — details about the Kubernetes clusters, including the API server address.
* **Users** — credentials used to authenticate with the cluster.
* **Contexts** — determine which user connects to which cluster.

By default, the kubeconfig file is located at:

```bash
~/.kube/config
```

When `kubectl` is used, it reads the kubeconfig file to determine **which Kubernetes cluster to connect to and how to authenticate**.

### Useful Commands

Check the current context:

```bash
kubectl config current-context
```

List available contexts:

```bash
kubectl config get-contexts
```

View the kubeconfig configuration:

```bash
kubectl config view

Kubernetes API Server

The Kubernetes API Server is the central management component of the Kubernetes control plane. It exposes the Kubernetes API and acts as the entry point for all communication with the cluster. It is responsible for authentication, authorization, request validation, and updating cluster state in etcd.

etcd

etcd is a distributed key-value database used by Kubernetes to store the cluster's state and configuration data. It stores information about Kubernetes objects such as nodes, pods, deployments, services, and namespaces. The Kubernetes API Server reads from and writes to etcd to maintain the cluster's desired state.

Kubernetes Scheduler

The Kubernetes Scheduler is a control plane component responsible for assigning Pods to worker nodes. It watches for newly created Pods that do not yet have a node assigned, evaluates available resources and scheduling constraints, and selects the most appropriate node on which the Pod should run.

Kubernetes Controller Manager

The Kubernetes Controller Manager is a control plane component responsible for monitoring the cluster and ensuring that the actual state matches the desired state. It continuously watches Kubernetes objects and takes corrective actions when differences are detected, such as creating replacement Pods or responding to node failures.```

Worker Node Components

1. kubelet
   - Watches for Pod assignments
   - Talks to API Server
   - Instructs container runtime

2. Container Runtime
   - Pulls images
   - Creates containers
   - Runs containers

3. kube-proxy
   - Handles networking
   - Creates Service networking rules
   - Enables Pod-to-Pod and Service communication

   # Kubernetes Services, Labels and Selectors

## Labels

Labels are key-value pairs attached to Kubernetes objects. They help identify, organize, and group resources.

Example:

```yaml
metadata:
  labels:
    app: nginx
```

* Label Key: `app`
* Label Value: `nginx`

Think of labels as tags or sticky notes attached to Kubernetes objects.

---

## Selectors

Selectors are used to find Kubernetes objects that have matching labels.

Example:

```yaml
selector:
  app: nginx
```

This selector tells Kubernetes:

> Find all objects with the label `app=nginx`.

### Label and Selector Relationship

```text
Service Selector
app=nginx
      |
      v
Pod A app=nginx  ✅
Pod B app=nginx  ✅
Pod C app=mysql  ❌
```

Only Pods whose labels match the selector are selected.

---

## Services

A Service provides a stable endpoint for accessing Pods.

Pods are temporary and their IP addresses can change when they are recreated. A Service remains stable and routes traffic to the correct Pods.

### Why Services are Needed

```text
Pod
- Temporary
- Can be replaced
- IP address can change

Service
- Stable
- Long-lived
- Routes traffic to Pods
```

Applications communicate with Services instead of directly using Pod IP addresses.

---

## How a Service Finds Pods

A Service uses a selector to find matching Pods.

Example:

```yaml
spec:
  selector:
    app: nginx
```

Pods:

```yaml
metadata:
  labels:
    app: nginx
```

The Service automatically discovers and routes traffic to matching Pods.

---

## Service YAML Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80
```

### Components

#### selector

```yaml
selector:
  app: nginx
```

Finds Pods with the label:

```yaml
app: nginx
```

#### port

```yaml
port: 80
```

The port exposed by the Service.

Clients connect to the Service using this port.

#### targetPort

```yaml
targetPort: 80
```

The port on the Pod/container that receives the traffic.

---

## port vs targetPort

Example:

```yaml
port: 80
targetPort: 8080
```

Traffic flow:

```text
Client
   |
   | 80
   v
Service
   |
   | 8080
   v
Container Application
```

The client connects to port 80 on the Service, and the Service forwards traffic to port 8080 on the container.

---

## Service Types

### ClusterIP (Default)

Used for internal communication within the Kubernetes cluster.

```text
Pod A
   |
   v
ClusterIP Service
   |
   v
Pod B
```

Accessible only from inside the cluster.

---

### NodePort

Exposes the Service on a port of each Worker Node.

Example:

```text
NodeIP:30080
```

Allows external access through the node's IP address.

---

### LoadBalancer

Used mainly in cloud environments such as AWS, Azure, and GCP.

```text
Internet
   |
Load Balancer
   |
Service
   |
Pods
```

Provides external access through a cloud load balancer.

---

## Key Takeaways

* Labels identify Kubernetes objects.
* Selectors find objects with matching labels.
* Services provide stable access to Pods.
* Services use selectors to locate backend Pods.
* `port` is the Service port.
* `targetPort` is the container port receiving traffic.
* ClusterIP is internal only.
* NodePort exposes a Service through a node.
* LoadBalancer exposes a Service externally through a cloud load balancer.

# Kubernetes Deployments and ReplicaSets

## Why Deployments Exist

Creating a Pod directly is not ideal for production environments because Pods are temporary.

Example:

```yaml
apiVersion: v1
kind: Pod
```

If the Pod is deleted, Kubernetes will not automatically recreate it unless another object manages it.

Deployments provide a higher-level mechanism for managing applications.

---

## Deployment

A Deployment is a Kubernetes object that manages application releases and updates.

Responsibilities:

* Creates and manages ReplicaSets
* Supports rolling updates
* Supports rollbacks
* Maintains the desired application configuration

Think:

```text
Deployment = Application Manager
```

---

## ReplicaSet

A ReplicaSet ensures that the desired number of Pods are running.

Example:

```yaml
replicas: 3
```

If one Pod is deleted:

```text
Desired Pods = 3
Actual Pods  = 2
```

The ReplicaSet creates a replacement Pod.

Think:

```text
ReplicaSet = Pod Count Manager
```

---

## Relationship Between Deployment and ReplicaSet

```text
Deployment
     |
     v
ReplicaSet
     |
     v
Pods
     |
     v
Containers
```

Deployment manages ReplicaSets.

ReplicaSets maintain the required number of Pods.

---

## Deployment YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx
```

---

## Important Fields

### replicas

```yaml
replicas: 3
```

Kubernetes attempts to keep three Pods running.

---

### selector

```yaml
selector:
  matchLabels:
    app: nginx
```

The Deployment manages Pods matching these labels.

---

### template

```yaml
template:
```

The Pod blueprint used when creating new Pods.

Every Pod created from the template receives the labels and container configuration defined inside it.

---

## Rolling Updates

Suppose the application image changes:

```yaml
image: myapp:v1
```

to

```yaml
image: myapp:v2
```

The Deployment performs a rolling update.

Example:

```text
Old ReplicaSet (v1)
      ↓
New ReplicaSet (v2)
```

Pods are gradually replaced instead of stopping everything at once.

Benefits:

* Reduced downtime
* Controlled upgrades
* Easier recovery

---

## Rollbacks

If the new version fails:

```text
v2 ❌
```

The Deployment can roll back to the previous version:

```text
v1 ✅
```

Example command:

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

## Common Commands

### View Deployments

```bash
kubectl get deployments
```

or

```bash
kubectl get deploy
```

### View ReplicaSets

```bash
kubectl get rs
```

### View Pods

```bash
kubectl get pods
```

### Deployment Details

```bash
kubectl describe deployment nginx-deployment
```

### Check Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment
```

### View Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

### Roll Back

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

## Key Takeaways

* Deployments manage application releases.
* ReplicaSets maintain the desired number of Pods.
* Deployments create and manage ReplicaSets.
* ReplicaSets create replacement Pods when needed.
* Deployments support rolling updates and rollbacks.
* Most production applications are deployed using Deployments rather than standalone Pods.

