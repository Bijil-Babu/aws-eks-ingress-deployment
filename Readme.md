## Production-Ready Django CI/CD on Amazon EKS
## Overview
This repository demonstrates a complete, end-to-end deployment of a containerized application on Amazon EKS, exposed using the AWS Load Balancer Controller (ALB Ingress).

The project covers:
* Amazon EKS cluster creation
* IAM, networking, and security setup
* AWS Load Balancer Controller installation
* Sample application deployment using Kubernetes manifests

  The repository is designed to be educational, reproducible, and production-aligned, following AWS and Kubernetes best practices.

  #Architecture Overview
  #Key Components
  * Amazon EKS (Kubernetes Control Plane)
  * AWS IAM & OIDC Provider
  * AWS Load Balancer Controller (ALB Ingress)
  * Kubernetes Deployment & Service
  * Amazon VPC & Security Groups
 
  #Prerequisites
  Before proceeding, ensure you have the following:
  * AWS Account
  * AWS CLI installed and configured
  * kubectl installed
  * eksctl installed
  * Helm installed
  * Docker (optional, for custom apps)

  #AWS Environment Setup
  1. Create AWS Account & IAM Users
  * Create an AWS account
  * Configure IAM users with least-privilege access
  * Enable MFA (recommended)
 
  2. Configure AWS CLI and kubectl
     ```
     aws configure
    ```
    
     ```
      aws eks update-kubeconfig --name <cluster-name>
    ```
