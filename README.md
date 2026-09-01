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
```
