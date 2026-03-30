don t modify single block ... rewrite final same content what i pasted now withe new forramt 

Got it, Asha — here is your fully rewritten README.md with correct formatting, no broken code blocks, no inconsistencies. Just copy–paste this entire content.

# 🚀 EKS CI/CD Pipeline with GitHub Actions, Terraform & AWS

## 📖 Overview

This project demonstrates a complete CI/CD pipeline that automates the process of building, storing, and deploying a containerized application to Amazon EKS.

The setup follows modern DevOps principles:

- Infrastructure as Code (Terraform)
- Secure authentication using OIDC (no AWS keys)
- Container-based deployment (Docker)
- Kubernetes orchestration (EKS)

The pipeline ensures that every code change is automatically built and deployed without manual intervention.

---

## 🏗️ Architecture Flow

GitHub Repository  
→ GitHub Actions (CI/CD Pipeline)  
→ AWS IAM (OIDC Role Assumption)  
→ Amazon ECR (Image Storage)  
→ Amazon EKS (Kubernetes Cluster)  
→ LoadBalancer Service  
→ End User (Browser Access)

---

## 🔄 Pipeline Execution Flow

### 1. Code Trigger

Any push to the `master` branch triggers the GitHub Actions workflow automatically.

---

### 2. Authentication (OIDC-Based Access)

Instead of storing AWS credentials, the pipeline uses OIDC:

- GitHub generates a temporary token  
- AWS validates the token using OIDC provider  
- IAM role `github-actions-eks-role` is assumed  

This approach provides:

- Zero hardcoded secrets  
- Temporary access tokens  
- Secure authentication model  

---

### 3. Build Phase (Docker Image Creation)

The GitHub Actions runner builds the Docker image:

- Application source is packaged  
- Dependencies are installed  
- Image is created and tagged  

```bash
docker build -t dev-eks-app .
4. Push Phase (ECR Integration)

The Docker image is pushed to Amazon ECR:

docker push 816069164153.dkr.ecr.us-east-1.amazonaws.com/dev-eks-app:latest

ECR acts as:

A private container registry
Central storage for images
Source for Kubernetes deployments
5. Cluster Access (EKS Connection)

Pipeline connects to the Kubernetes cluster:

aws eks update-kubeconfig --region us-east-1 --name EKS-DEV

This allows GitHub Actions to run kubectl commands.

6. Deployment Phase (Kubernetes Apply)

Kubernetes manifests are applied:

kubectl apply -f k8s/

This creates:

Deployment → manages Pods
Service → exposes application
7. Runtime Execution (EKS)
Kubernetes pulls image from ECR
Pods are created inside worker nodes
Containers start running the application

Key idea:

Docker builds the image
Kubernetes runs the container
8. External Exposure (LoadBalancer)

A Kubernetes Service of type LoadBalancer:

AWS provisions an ELB automatically
Traffic is routed to Pods
Application becomes publicly accessible
🔐 Access Control & Security
IAM Role (GitHub Actions)
Created via Terraform
Assumed using OIDC
Grants access to:
ECR (push images)
EKS (cluster operations)
Kubernetes RBAC Mapping

IAM role is mapped in aws-auth:

mapRoles:
  - rolearn: arn:aws:iam::816069164153:role/github-actions-eks-role
    username: github-actions
    groups:
      - system:masters

This enables deployment access inside the cluster.

🧩 Infrastructure Setup (Terraform)

The infrastructure is modular and reusable:

VPC (networking layer)
EKS Cluster (control plane)
Node Groups (compute layer)
ECR Repository (image storage)
IAM Roles (security)

Supports environment-based deployments (dev, sit, prod).

📸 Project Screenshots
Repository Structure

CI/CD Pipeline Success

Pipeline Execution Steps

ECR Repository

ECR Image

Pods and Service (External IP)

Application Output

🌐 Application Access

http://<LoadBalancer-URL>

🧠 Concepts Demonstrated
CI/CD automation using GitHub Actions
Secure AWS authentication with OIDC
Docker image lifecycle management
Kubernetes Deployment and Service usage
Infrastructure provisioning with Terraform

 Possible Enhancements
Helm-based deployments
Multi-environment pipelines
HTTPS using Ingress + ACM
Monitoring (Prometheus & Grafana)
Versioned image tagging strategy
👩‍💻 Author

Asha 
— DevOps & AI-DevOps Engineer

⭐ Summary

This project showcases a real-world DevOps pipeline where infrastructure, security, CI/CD, and Kubernetes work together to deliver a fully automated deployment system.
