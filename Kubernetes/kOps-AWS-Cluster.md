# AWS Kubernetes Cluster with kOps

## Project Overview

This project documents the independent deployment of a Kubernetes cluster on Amazon Web Services (AWS) using kOps.

The objective was to move beyond local Kubernetes experimentation with Minikube and provision a Kubernetes cluster on AWS, including DNS configuration, an S3-based kOps state store, AWS networking, EC2 instances, IAM roles, security groups, Kubernetes networking, and cluster validation.

This cluster was built independently as a hands-on DevOps learning project.

---

## Objectives

* Deploy Kubernetes on AWS using kOps.
* Configure a DNS zone for the Kubernetes cluster.
* Use Amazon S3 as the kOps cluster state store.
* Provision a control-plane node and worker nodes.
* Understand the AWS infrastructure created by kOps.
* Verify Kubernetes networking and system components.
* Validate the completed cluster using kOps and kubectl.
* Document the deployment process and lessons learned.

---

## Environment

| Component          | Configuration         |
| ------------------ | --------------------- |
| Cloud Provider     | AWS                   |
| Region             | `us-east-1`           |
| Availability Zone  | `us-east-1a`          |
| Kubernetes Tool    | kOps                  |
| kOps Version       | `1.36.2`              |
| Kubernetes Version | `v1.36.4`             |
| Control Plane      | 1 × `t3.medium`       |
| Worker Nodes       | 2 × `t3.medium`       |
| Cluster Name       | `k8s.eno-devops.xyz`  |
| DNS Zone           | `k8s.eno-devops.xyz`  |
| kOps State Store   | `s3://kopsstates3101` |
| VPC CIDR           | `172.20.0.0/16`       |
| Networking         | Cilium                |
| Operating System   | Ubuntu 24.04          |

---

## Architecture

The cluster consists of:

```text
                    Internet
                       |
                       |
                Internet Gateway
                       |
                       |
              AWS VPC 172.20.0.0/16
                       |
                Public Subnet
                 us-east-1a
                       |
          +------------+------------+
          |                         |
    Control Plane              Worker Nodes
     t3.medium              2 × t3.medium
          |                         |
          +------------+------------+
                       |
                  Kubernetes
                       |
        +--------------+--------------+
        |              |              |
      Cilium        CoreDNS        EBS CSI
```

kOps also provisioned supporting AWS resources including IAM roles and instance profiles, security groups, etcd EBS volumes, an SQS queue, EventBridge rules, an Internet Gateway, route tables, and the required Kubernetes bootstrap configuration.

---

## DNS Configuration

The Kubernetes cluster uses:

```text
k8s.eno-devops.xyz
```

DNS delegation was verified using:

```bash
dig NS k8s.eno-devops.xyz
```

The query returned four AWS Route 53 nameservers:

```text
ns-951.awsdns-54.net.
ns-1447.awsdns-52.org.
ns-1736.awsdns-25.co.uk.
ns-401.awsdns-50.com.
```

This confirmed that the delegated DNS zone was resolving through AWS nameservers.

---

## kOps State Store

The kOps state store was configured using Amazon S3:

```bash
export KOPS_STATE_STORE=s3://kopsstates3101
export NAME=k8s.eno-devops.xyz
```

The configuration was verified with:

```bash
echo $KOPS_STATE_STORE
echo $NAME
```

The state store allows kOps to maintain the configuration and state information required to manage the Kubernetes cluster.

---

## Cluster Creation

The cluster configuration was created using:

```bash
kops create cluster \
  --name=k8s.eno-devops.xyz \
  --state=s3://kopsstates3101 \
  --zones=us-east-1a \
  --node-count=2 \
  --node-size=t3.medium \
  --control-plane-size=t3.medium \
  --dns-zone=k8s.eno-devops.xyz
```

kOps generated the AWS infrastructure configuration, including the VPC, subnet, security groups, IAM roles, EC2 launch templates, autoscaling groups, etcd volumes, DNS configuration, and Kubernetes bootstrap resources.

---

## Deploying the Cluster

The cluster configuration was applied with:

```bash
kops update cluster \
  --name=k8s.eno-devops.xyz \
  --yes \
  --admin
```

kOps generated the required certificates and configuration, exported the Kubernetes kubeconfig, and configured the local `kubectl` context.

The cluster name was confirmed with:

```bash
kops get cluster
```

Result:

```text
NAME                    CLOUD   ZONES
k8s.eno-devops.xyz      aws     us-east-1a
```

---

## Cluster Validation

The cluster was validated using:

```bash
kops validate cluster --wait 10m
```

Validation completed successfully:

```text
INSTANCE GROUPS
NAME                            ROLE            MACHINETYPE     MIN     MAX    SUBNETS
control-plane-us-east-1a        ControlPlane    t3.medium       1       1      us-east-1a
nodes-us-east-1a                Node            t3.medium       2       2      us-east-1a

NODE STATUS
NAME                    ROLE              READY
i-01e2e7ff436dd51e5     node              True
i-0214d3b96d8460547     node              True
i-09e7d91d7e2337d58     control-plane     True

Your cluster k8s.eno-devops.xyz is ready
```

---

## Kubernetes Node Verification

The Kubernetes nodes were verified using:

```bash
kubectl get nodes
```

Result:

```text
NAME                  STATUS   ROLES           VERSION
i-01e2e7ff436dd51e5   Ready    node            v1.36.4
i-0214d3b96d8460547   Ready    node            v1.36.4
i-09e7d91d7e2337d58   Ready    control-plane   v1.36.4
```

The cluster therefore contains:

* 1 control-plane node
* 2 worker nodes
* All nodes in `Ready` state
* Kubernetes `v1.36.4`

---

## Kubernetes System Components

The cluster system pods were verified with:

```bash
kubectl get pods -A
```

Important components confirmed as running included:

* AWS Cloud Controller Manager
* AWS Node Termination Handler
* Cilium
* Cilium Operator
* CoreDNS
* DNS Controller
* AWS EBS CSI Controller
* AWS EBS CSI Node components
* etcd manager
* kOps controller
* Kubernetes API Server
* Kubernetes Controller Manager
* Kubernetes Scheduler

All listed system components were running successfully at the time of verification.

---

## AWS Resources Provisioned by kOps

The kOps configuration created and managed several AWS resources.

### Networking

* VPC: `172.20.0.0/16`
* Public subnet in `us-east-1a`
* Internet Gateway
* Route table
* DHCP options
* IPv6 configuration

### Compute

* 1 control-plane EC2 instance
* 2 worker EC2 instances
* EC2 launch templates
* Auto Scaling Groups

### Storage

* 20 GB encrypted EBS volume for etcd events
* 20 GB encrypted EBS volume for etcd main

### Identity and Access

* Control-plane IAM role
* Worker-node IAM role
* IAM instance profiles
* IAM policies

### Kubernetes Infrastructure

* Cilium networking
* CoreDNS
* AWS Cloud Controller Manager
* AWS EBS CSI driver
* AWS Node Termination Handler
* kOps controller

### Event Handling

kOps also configured:

* Amazon SQS
* Amazon EventBridge rules
* Auto Scaling lifecycle handling
* EC2 state-change handling
* Spot interruption handling
* AWS Health scheduled-change handling

---

## Security Considerations

The cluster was created as a learning environment rather than a production deployment.

During the lab, SSH access was configured to allow access from `0.0.0.0/0`.

This is convenient for temporary testing but is **not recommended for production environments** because it exposes SSH to the entire internet.

For a production-oriented implementation, SSH access should be restricted to trusted source IP addresses or replaced with a more secure administrative access mechanism.

Other production security considerations would include:

* Private subnets for worker nodes where appropriate
* Restricted security-group rules
* Least-privilege IAM policies
* Strong identity and access management
* Network segmentation
* Secrets management
* Encryption
* Centralized logging and monitoring
* Controlled administrative access

---

## Troubleshooting and Learning Notes

One of the main learning objectives of this project was understanding the difference between the different types of credentials involved in a cloud/Kubernetes environment.

### AWS credentials

AWS Access Key ID and Secret Access Key are used by the AWS CLI to authenticate API requests to AWS.

### SSH keys

SSH key pairs are used for secure SSH authentication.

The private key remains on the client machine while the corresponding public key is placed on the target system or service.

### GitHub SSH authentication

The same EC2 environment was configured to authenticate with GitHub using SSH.

The public key was added to GitHub while the private key remained on the EC2 instance.

This allowed the existing `kubernetes-labs` GitHub repository to be cloned securely using:

```bash
git clone git@github.com:ukoeno/kubernetes-labs.git
```

---

## Production Considerations

This implementation is intentionally optimized for hands-on learning.

A production Kubernetes environment would normally require additional considerations such as:

* Multiple Availability Zones
* Highly available control-plane architecture
* Private networking
* Restricted administrative access
* More carefully designed security groups
* Production-grade monitoring and logging
* Backup and disaster recovery
* Resource sizing based on workload requirements
* Network and storage architecture appropriate for production workloads
* Formal IAM and secrets-management strategy

---

## Outcome

The AWS Kubernetes cluster was successfully deployed using kOps.

Final verification confirmed:

```text
Cluster:       k8s.eno-devops.xyz
Cloud:         AWS
Availability:  us-east-1a
Control Plane: 1
Worker Nodes:  2
Node Status:   Ready
Kubernetes:    v1.36.4
Validation:    Passed
```

The project provided hands-on experience with AWS infrastructure provisioning, Kubernetes cluster architecture, kOps, DNS delegation, S3 state management, IAM, EC2, VPC networking, security groups, Cilium, CoreDNS, EBS CSI, and Kubernetes cluster validation.

