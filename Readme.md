## AWS EKS Application Deployment with ALB Ingress

## Overview
This repository demonstrates a complete, end-to-end deployment of a containerized application on Amazon Elastic Kubernetes Service (EKS), exposed using the AWS Load Balancer Controller (ALB Ingress).

The project covers:
* Amazon EKS cluster creation
* IAM, networking, and security setup
* AWS Load Balancer Controller installation
* Sample application deployment using Kubernetes manifests

The repository is designed to be educational, reproducible, and production-aligned, following AWS and Kubernetes best practices.

---

## Architecture Overview

### Key Components
* Amazon EKS (Managed Kubernetes Control Plane)
* AWS IAM & OIDC Provider
* AWS Load Balancer Controller (ALB Ingress)
* Kubernetes Deployment & Service
* Amazon VPC & Security Groups

### Traffic Flow
User → ALB (Ingress) → Kubernetes Service → Application Pod

---

## Prerequisites

Before proceeding, ensure you have the following:
* AWS Account
* AWS CLI installed and configured
* kubectl installed
* eksctl installed
* Helm installed
* Docker (optional, for custom applications)

---

## AWS Environment Setup

### 1. Create AWS Account & IAM Users
* Create an AWS account
* Configure IAM users with least-privilege access
* Enable Multi-Factor Authentication (MFA) (recommended)

---

### 2. Configure AWS CLI and kubectl

Configure AWS CLI credentials:
aws configure


Update kubeconfig for EKS:
aws eks update-kubeconfig --name <cluster-name>


Verify cluster connectivity:
kubectl get nodes


---

## Networking and Security Configuration

* Create an Amazon VPC with public and private subnets
* Configure Security Groups for EKS worker nodes
* Attach an Internet Gateway (IGW)
* Update route tables for outbound internet access
* Assign appropriate IAM roles to EKS worker nodes

---

## EKS Cluster Installation

### Install EKS Using Fargate
eksctl create cluster --name demo-cluster --region us-east-1 --fargate


This command provisions:
* EKS control plane
* Fargate profiles
* Managed networking resources

---

## IAM OIDC Provider Configuration

Set the cluster name:
export cluster_name=demo-cluster


Retrieve OIDC ID:
```
oidc_id=$(aws eks describe-cluster --name $cluster_name
--query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)
```

Check if OIDC provider exists:
```
aws iam list-open-id-connect-providers | grep $oidc_id
```

If not present, associate the OIDC provider:
```
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```

---

## AWS Load Balancer Controller Setup

### Download IAM Policy
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json


### Create IAM Policy
aws iam create-policy
--policy-name AWSLoadBalancerControllerIAMPolicy
--policy-document file://iam_policy.json


### Create IAM Service Account
```
eksctl create iamserviceaccount
--cluster=<your-cluster-name>
--namespace=kube-system
--name=aws-load-balancer-controller
--role-name AmazonEKSLoadBalancerControllerRole
--attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy
--approve
```
---

## Deploy AWS Load Balancer Controller

Add Helm repository:
```
helm repo add eks https://aws.github.io/eks-charts
```

Update Helm repositories:
```
helm repo update
```

Install the controller:
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller
-n kube-system
--set clusterName=<your-cluster-name>
--set serviceAccount.create=false
--set serviceAccount.name=aws-load-balancer-controller
--set region=<your-region>
--set vpcId=<your-vpc-id>
```


Verify deployment:
```
kubectl get deployment -n kube-system aws-load-balancer-controller

```

---

## Sample Application Deployment

### Deployment Manifest

Create `deploy.yaml`:
```
apiVersion: apps/v1
kind: Deployment
metadata:
name: eks-sample-linux-deployment
labels:
app: eks-sample-linux-app
spec:
replicas: 3
selector:
matchLabels:
app: eks-sample-linux-app
template:
metadata:
labels:
app: eks-sample-linux-app
spec:
containers:
- name: nginx
image: public. ecr.aws/nginx/nginx:1.23
ports:
- containerPort: 80
```

Deploy the application:
```
kubectl apply -f deploy.yaml
```


---

### Service Manifest

Create `service.yaml`:
```
apiVersion: v1
kind: Service
metadata:
name: eks-sample-linux-service
spec:
selector:
app: eks-sample-linux-app
ports:
- protocol: TCP
port: 80
targetPort: 80
```


Deploy the service:
```
kubectl apply -f service.yaml
```


---

## Cluster Cleanup

To delete the EKS cluster and associated resources:
```
eksctl delete cluster --name demo-cluster --region us-east-1
```

---

## Key Learning Outcomes

* End-to-end Amazon EKS setup
* Secure IAM and OIDC integration
* ALB Ingress controller deployment
* Kubernetes workload deployment
* AWS-native Kubernetes networking

---
![2048_game](https://github.com/user-attachments/assets/a624bcbb-6925-48aa-b257-f0352c954f0c)

