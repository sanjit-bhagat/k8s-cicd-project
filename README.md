# Automated Kubernetes CI/CD Pipeline

An end-to-end DevOps project that automates application testing, Docker image building, publishing to Docker Hub, and deployment to a local Kubernetes cluster using GitHub Actions.

## Project Overview

This project demonstrates how to automate the deployment of a web application using a CI/CD pipeline. Whenever code is pushed to the `main` branch, GitHub Actions builds and tests the application, pushes the Docker image to Docker Hub, and deploys the new version to Kubernetes.

## Tools & Technologies

- **Git & GitHub** — Source code management
- **GitHub Actions** — CI/CD automation
- **Docker** — Containerization
- **Docker Hub** — Container image registry
- **Kubernetes** — Application deployment and management
- **Kind** — Local Kubernetes cluster
- **NGINX** — Web server
- **YAML** — Kubernetes and workflow configuration
- **PowerShell** — Local Windows administration

## Architecture

```text
Developer
    |
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    v
Build Docker Image
    |
    v
Test Web Application
    |
    v
Push Image to Docker Hub
    |
    v
Deploy to Kubernetes
    |
    v
Kubernetes Deployment
    |
    v
4 Application Pods
    |
    v
Kubernetes Service
    |
    v
Web Application
```

## CI/CD Workflow

1. **Code Push:** A code change is pushed to the `main` branch.
2. **Build:** GitHub Actions builds a Docker image from the application Dockerfile.
3. **Test:** The pipeline starts a test container and checks whether the web application responds successfully.
4. **Publish:** The Docker image is pushed to Docker Hub with a commit-specific SHA tag and a `latest` tag.
5. **Deploy:** A self-hosted Windows runner updates the Kubernetes Deployment with the commit-specific image.
6. **Verify:** The pipeline waits for the Kubernetes rollout to complete successfully.

## Kubernetes Features

- Deployment management using Kubernetes Deployments
- Four application replicas
- NodePort Service for application access
- Startup, liveness, and readiness probes
- CPU and memory requests and limits
- Rolling updates through Kubernetes Deployments
- Container images tagged with Git commit SHA for version tracking

## Project Structure

```text
k8s-cicd-project/
├── app/
│   ├── Dockerfile
│   └── index.html
├── k8s/
│   ├── namespace.yaml
│   ├── deployment.yaml
│   └── service.yaml
├── .github/
│   └── workflows/
│       └── ci-cd.yaml
└── README.md
```

## Prerequisites

Install or configure the following:

- Docker Desktop
- kubectl
- Kind
- Git
- A GitHub repository
- A Docker Hub account
- A running self-hosted GitHub Actions runner with access to the Kind cluster

## Setup Instructions

### 1. Clone the repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd k8s-cicd-project
```

### 2. Build the Docker image locally

Replace `YOUR_DOCKERHUB_USERNAME` with your Docker Hub username.

```bash
docker build -t YOUR_DOCKERHUB_USERNAME/k8s-cicd-app:latest ./app
```

### 3. Create the Kubernetes cluster

If you do not already have a Kind cluster:

```bash
kind create cluster --name devops-cluster
```

### 4. Apply the Kubernetes manifests

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

### 5. Configure GitHub Actions secrets

Add the following repository secrets under **Settings → Secrets and variables → Actions**:

| Secret | Purpose |
|---|---|
| `DOCKERHUB_USERNAME` | Docker Hub username |
| `DOCKERHUB_TOKEN` | Docker Hub access token |

Never commit access tokens or passwords to the repository.

### 6. Configure the self-hosted runner

Configure a self-hosted Windows runner with the labels used by the workflow:

```text
self-hosted
Windows
X64
```

Ensure the runner can access the correct Kind Kubernetes context and execute `kubectl`.

### 7. Trigger the pipeline

Push a change to the `main` branch:

```bash
git add .
git commit -m "Update web application"
git push origin main
```

Open the **Actions** tab in GitHub to monitor the workflow.

## Verification Commands

Check the Kubernetes Deployment:

```bash
kubectl get deployment web-app -n cicd-project
```

Check application pods:

```bash
kubectl get pods -n cicd-project
```

Check the Service:

```bash
kubectl get services -n cicd-project
```

Check the deployed image:

```bash
kubectl get deployment web-app -n cicd-project -o jsonpath="{.spec.template.spec.containers[0].image}"
```

Check rollout status:

```bash
kubectl rollout status deployment/web-app -n cicd-project
```

## Key Learning Outcomes

- Built an automated CI/CD pipeline with GitHub Actions
- Containerized a web application using Docker
- Published versioned images to Docker Hub
- Deployed an application to Kubernetes using a self-hosted runner
- Configured Kubernetes health probes and resource limits
- Verified rolling deployments and application pod health
- Practiced troubleshooting workflow syntax and deployment issues

## Future Improvements

- Add automated unit tests
- Add monitoring with Prometheus and Grafana
- Add centralized logging
- Add security scanning for Docker images
- Deploy to a managed Kubernetes service such as Amazon EKS
- Add infrastructure provisioning with Terraform

## Author

**Sanjit Bhagat**

Aspiring DevOps Engineer

