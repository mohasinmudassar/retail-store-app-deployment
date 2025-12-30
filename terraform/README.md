# Retail Store Infrastructure on AWS (Terraform + EKS)

This repository contains Terraform infrastructure code to deploy a **Retail Store microservices application** on **AWS EKS**, following modern **DevOps and GitOps practices**.

The project focuses on:
- Infrastructure provisioning using Terraform
- Running a production-ready Amazon EKS cluster
- Installing and managing Kubernetes add-ons
- Deploying applications using **Argo CD (GitOps)**

---

## What This Project Does

This setup:
- Creates a VPC with public and private subnets
- Provisions an EKS cluster with auto-managed compute
- Installs essential Kubernetes add-ons
- Deploys Argo CD using Helm
- Uses GitOps to manage application deployments

All infrastructure is fully automated using Terraform.

---

## Tech Stack

- **Cloud Provider**: AWS  
- **Infrastructure as Code**: Terraform  
- **Container Orchestration**: Amazon EKS  
- **GitOps**: Argo CD  
- **Ingress**: NGINX Ingress Controller  

---

## Terraform Directory Structure

```text
terraform/
├── main.tf                 # Core infrastructure (VPC + EKS)
├── versions.tf             # Terraform & provider versions
├── variables.tf            # Input variables
├── locals.tf               # Local values and naming
├── security.tf             # Security groups and rules
├── addons.tf               # EKS add-ons (Ingress, cert-manager)
├── argocd.tf               # Argo CD installation (Helm)
├── outputs.tf              # Useful outputs
├── terraform.tfvars.example
└── README.md
```

Each file has a single responsibility, keeping the codebase clean and maintainable.

---

## Prerequisites

Make sure the following tools are installed and configured:

- AWS CLI (configured with valid credentials)
- Terraform >= 1.0
- kubectl
- Helm

Verify with:
```bash
aws sts get-caller-identity
terraform version
kubectl version --client
helm version
```

---

## Configuration

### Create variables file

```bash
cp terraform.tfvars.example terraform.tfvars
```

Example configuration:

```hcl
aws_region                = "us-west-2"
cluster_name              = "retail-store"
environment               = "dev"
kubernetes_version        = "1.33"
vpc_cidr                  = "10.0.0.0/16"
enable_single_nat_gateway = true
enable_monitoring         = false
```

A random 4-character suffix is automatically added to the cluster name to avoid conflicts.

---

## Deploying the Infrastructure

### Initialize Terraform
```bash
terraform init
```

### Review the plan
```bash
terraform plan
```

### Apply the configuration
```bash
terraform apply
```

---

## Accessing the EKS Cluster

Configure kubectl after deployment:

```bash
aws eks update-kubeconfig   --region us-west-2   --name <cluster-name>
```

Verify:
```bash
kubectl get nodes
```

---

## Accessing Argo CD

### Get admin password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret   -o jsonpath='{.data.password}' | base64 -d
```

### Port-forward Argo CD UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- URL: https://localhost:8080  
- Username: admin  
- Password: (from the command above)

---

## What Gets Deployed

### AWS & Kubernetes
- VPC with public and private subnets
- NAT Gateway (cost-optimized for dev)
- EKS cluster with auto-managed compute
- IAM roles and security groups
- KMS encryption for secrets

### Kubernetes Add-ons
- NGINX Ingress Controller
- cert-manager
- Argo CD (Helm-based installation)

### Applications
- Retail Store microservices
- Managed entirely via Argo CD (GitOps)

---

## Cleanup

To destroy all resources:

```bash
terraform destroy
```

This will remove the EKS cluster, VPC, and all related resources.

---

## Notes

- Terraform state files are intentionally excluded from Git
- Infrastructure and application delivery are clearly separated
- Designed for learning, demos, and portfolio use
