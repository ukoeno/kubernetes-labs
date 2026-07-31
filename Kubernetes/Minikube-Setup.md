# Minikube Setup

Status: In Progress

## Objective

- Install Minikube
- Install kubectl
- Start local Kubernetes cluster
- Deploy first pod
- Expose pod and access application
# Minikube Setup Lab

## Objective
Create a local Kubernetes cluster using Minikube and Docker.

## Environment
- OS: Windows 11
- Docker Driver: Docker
- Minikube Version: v1.39.0
- Kubernetes Version: v1.35.1

## Commands

minikube start --driver=docker

kubectl get nodes

kubectl cluster-info

kubectl get pods -A

## Verification

Node Status:
minikube Ready

Control Plane:
Running

System Pods:
- coredns
- etcd
- kube-apiserver
- kube-controller-manager
- kube-proxy
- kube-scheduler
- storage-provisioner

## Issues Encountered

Initial startup reported:
- GUEST_NOT_FOUND
- icacls permission error

Verification with:
- docker ps
- minikube status

showed the cluster was actually running successfully.

## Outcome

Successfully deployed and verified a single-node Kubernetes cluster using Minikube.
