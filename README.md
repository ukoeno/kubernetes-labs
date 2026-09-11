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
