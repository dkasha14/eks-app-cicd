# 🚀 End-to-End EKS CI/CD Pipeline using GitHub Actions, Terraform & AWS

## 📌 Project Overview

This project demonstrates a complete, production-style CI/CD pipeline that automates building, packaging, and deploying an application to Amazon EKS using GitHub Actions.

It follows modern DevOps practices:
- Infrastructure as Code (Terraform)
- Secure authentication using OIDC (no AWS keys)
- Containerization using Docker
- Deployment using Kubernetes

The pipeline eliminates manual deployment and enables a fully automated, secure, and scalable workflow.

---

## 🧱 High-Level Architecture

GitHub → GitHub Actions → AWS IAM (OIDC Role) → Amazon ECR → Amazon EKS → LoadBalancer → End User

---

## 🔄 End-to-End Workflow

### 1. Code Push (Trigger)

When code is pushed to the `master` branch, GitHub Actions automatically triggers the pipeline.

---

### 2. Secure Authentication using OIDC

Instead of storing AWS credentials, GitHub uses OpenID Connect (OIDC):

- GitHub generates a temporary identity token
- AWS validates the token via OIDC provider
- IAM role `github-actions-eks-role` is assumed securely

This ensures:
- No hardcoded secrets
- Temporary credentials
- High security

---

### 3. Docker Image Build (CI Phase)

Inside GitHub Actions runner:

- Application is packaged into a Docker image
- Dependencies are installed
- Image is tagged

```bash
docker build -t dev-eks-app .
4. Push Image to Amazon ECR

The Docker image is pushed to ECR:

docker push 816069164153.dkr.ecr.us-east-1.amazonaws.com/dev-eks-app:latest

ECR:

Stores container images
Acts as private registry
Allows secure image pulling by Kubernetes
5. Connect to EKS Cluster

Pipeline connects to EKS:

aws eks update-kubeconfig --region us-east-1 --name EKS-DEV

This enables kubectl access.

6. Kubernetes Deployment (CD Phase)

Pipeline applies manifests:

kubectl apply -f k8s/

This creates:

Deployment (Pods)
Service (LoadBalancer)
7. Container Execution in EKS

Kubernetes pulls image from ECR and runs containers inside Pods.

Key concept:

Docker builds image
Kubernetes runs containers
8. Service Exposure (LoadBalancer)

Kubernetes Service:

Type: LoadBalancer
AWS provisions ELB
External traffic routed to Pods
🔐 IAM & Access Control
IAM Role (GitHub Actions)
Created via Terraform
Assumed using OIDC
Permissions:
ECR access
EKS access
Kubernetes RBAC (aws-auth)
mapRoles:
  - rolearn: arn:aws:iam::816069164153:role/github-actions-eks-role
    groups:
      - system:masters

Allows GitHub Actions to deploy to cluster.

📦 Infrastructure (Terraform)

Modular design:

VPC (networking)
EKS cluster
Node groups
ECR repository
IAM roles

Supports multiple environments (dev, sit, prod).

📸 Screenshots
1️⃣ Repository Structure

2️⃣ Pipeline Success

3️⃣ Pipeline Steps

4️⃣ ECR Repository

5️⃣ ECR Image

6️⃣ Pods & Service

7️⃣ Application Output

🌐 Application Access
http://<LoadBalancer-URL>
🧠 Key Concepts Demonstrated
CI/CD automation using GitHub Actions
Secure AWS authentication using OIDC
Docker image lifecycle (build → push)
Kubernetes deployment and service exposure
Infrastructure as Code using Terraform
🚀 Future Enhancements
Helm-based deployments
Multi-environment pipelines
HTTPS with Ingress + ACM
Monitoring (Prometheus, Grafana)
Image versioning (instead of latest)
👩‍💻 Author

Asha 
– DevOps & AI-DevOps Engineer

⭐ Final Note

This project demonstrates a real-world DevOps pipeline integrating CI/CD, cloud infrastructure, containerization, and Kubernetes into a single automated workflow.
