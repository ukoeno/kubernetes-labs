# Deployments and Services

## Objective

Deploy an application to Kubernetes and expose it for access.

## Environment

* Kubernetes Version: v1.35.1
* Minikube Version: v1.39.0
* Driver: Docker
* OS: Windows 11

## Create Deployment
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.53 -- /agnhost netexec --http-port=8080

## Verify Deployment
kubectl get deployments
kubectl get pods

Result:

* Deployment: hello-node
* Pod Status: Running (1/1)

## Expose Application
bash
kubectl expose deployment hello-node --type=NodePort --port=8080

Verify:
bash
kubectl get services

Result:

* Service Name: hello-node
* Service Type: NodePort
* Port: 8080

## Access Application
minikube service hello-node

Minikube created a local URL and opened the application in the browser.

## Concepts Learned

### Deployment

A Deployment manages application Pods and ensures the desired number of replicas are running.

### Pod

A Pod is the smallest deployable unit in Kubernetes and contains one or more containers.

### Service

A Service provides network access to Pods and enables communication with applications.

### NodePort

A NodePort Service exposes an application on a port accessible from outside the cluster.

## Troubleshooting

### Git Bash Path Conversion

Issue:

Git Bash converted:
/agnhost

to:
C:/Program Files/Git/agnhost

Result:

* Pod entered RunContainerError
* Pod entered CrashLoopBackOff

Resolution:
export MSYS_NO_PATHCONV=1
The deployment was recreated successfully and the Pod reached the Running state.

## Outcome

Successfully deployed an application to Kubernetes, exposed it using a NodePort Service, and accessed it through Minikube.
