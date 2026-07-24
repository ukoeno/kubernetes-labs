# Minikube Setup on Windows

**Status**: Completed

## Objective
- Install Minikube and kubectl
- Start a local Kubernetes cluster
- Deploy first pod
- Expose the pod and access the application

## Prerequisites
- Windows 10 or 11
- Chocolatey package manager
- Administrator rights
- (Optional) Docker Desktop or Hyper-V enabled

## Installation

### 1. Install Minikube + kubectl

```powershell
# Run in PowerShell as Administrator
choco install minikube kubernetes-cli -y
