# SECTION 2 — Real-Time Projects

# Project 10: Deploy Super Mario on Kubernetes using Terraform

> **Project Level:** 🔴 Expert
> **Architecture Type:** Containerized Application Deployment on Managed Kubernetes
> **AWS Services & Tools:** Amazon EKS + Terraform + Kubernetes + LoadBalancer Service

---

# Overview

In this real-time project, you will deploy the **Super Mario game container** on **Amazon Elastic Kubernetes Service (EKS)** using **Terraform** for infrastructure provisioning.

The project demonstrates a complete Kubernetes deployment workflow:

* Provision an EKS cluster using Terraform
* Configure Kubernetes access using AWS CLI
* Deploy a containerized application using Kubernetes manifests
* Expose the application using a LoadBalancer service
* Access the game through an external AWS load balancer endpoint
* Clean up all deployed resources

---

# Learning Objectives

After completing this project, you will be able to:

* ✅ Provision Kubernetes infrastructure using Terraform
* ✅ Create and manage an Amazon EKS cluster
* ✅ Configure `kubectl` access with AWS EKS
* ✅ Deploy applications using Kubernetes YAML manifests
* ✅ Create Kubernetes Deployments and Services
* ✅ Expose applications externally using LoadBalancer services
* ✅ Remove Kubernetes and AWS resources safely

---

# Prerequisites

Before starting this project, ensure you have:

* AWS Account
* IAM permissions for:

  * Amazon EKS
  * EC2
  * IAM
  * VPC
* Installed tools:

```text
Terraform
AWS CLI
kubectl
```

* Docker container image access

---

# Architecture Overview

```text
                         User Browser

                              │

                              ▼

                    AWS Load Balancer

                              │

                              ▼

                  Kubernetes Service

                  mario-service
                  Type: LoadBalancer

                              │

                              ▼

                  Kubernetes Deployment

                        mario

                    Replicas: 2

                              │

                              ▼

                    Mario Containers

              sevenajay/mario:latest

                              │

                              ▼

                    Amazon EKS Cluster
```

---

# Deployment Workflow

```text
Terraform

   │

   ▼

Amazon EKS Cluster

   │

   ▼

kubectl Configuration

   │

   ▼

Kubernetes Deployment

   │

   ▼

LoadBalancer Service

   │

   ▼

External IP

   │

   ▼

Super Mario Game
```

---

# Step 1 — Provision EKS with Terraform

Use Terraform to create the Kubernetes infrastructure.

---

## Initialize Terraform

Run:

```bash
terraform init
```

---

## Deploy EKS Cluster

Execute:

```bash
terraform apply -auto-approve
```

Terraform provisions:

* EKS Cluster
* Worker Nodes
* Networking components
* IAM roles

---

## Configure kubectl Access

Update Kubernetes configuration:

```bash
aws eks update-kubeconfig \
--name devops-eks \
--region ap-south-1
```

---

## Verify Cluster Connectivity

Check Kubernetes nodes:

```bash
kubectl get nodes
```

Expected output:

```text
NAME                 STATUS
worker-node-1        Ready
worker-node-2        Ready
```

---

# Step 2 — Create Mario Deployment YAML

Create file:

```text
mario-deployment.yaml
```

Add the following configuration:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: mario
  labels:
    app: mario

spec:
  replicas: 2

  selector:
    matchLabels:
      app: mario

  template:
    metadata:
      labels:
        app: mario

    spec:
      containers:
      - name: mario
        image: sevenajay/mario:latest
        ports:
        - containerPort: 8080
```

---

# Deployment Configuration Explanation

| Configuration   | Description              |
| --------------- | ------------------------ |
| Deployment Name | `mario`                  |
| Replicas        | `2`                      |
| Container Image | `sevenajay/mario:latest` |
| Container Port  | `8080`                   |

---

## Kubernetes Deployment Flow

```text
Deployment

    │

    ▼

ReplicaSet

    │

    ▼

Pods

    │

    ▼

Mario Containers
```

---

# Step 3 — Create Mario Service YAML

Create file:

```text
mario-service.yaml
```

Add:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: mario-service

spec:

  selector:
    app: mario

  ports:
  - port: 80
    targetPort: 8080

  type: LoadBalancer
```

---

# Service Configuration Explanation

| Setting        | Value           |
| -------------- | --------------- |
| Service Name   | `mario-service` |
| Service Type   | LoadBalancer    |
| External Port  | 80              |
| Container Port | 8080            |

---

# Kubernetes Service Flow

```text
Internet

   │

   ▼

AWS Load Balancer

   │

   ▼

mario-service

   │

   ▼

Mario Pods
```

---

# Step 4 — Deploy Mario Application to EKS

Apply the Kubernetes deployment:

```bash
kubectl apply -f mario-deployment.yaml
```

Apply the service:

```bash
kubectl apply -f mario-service.yaml
```

---

# Verify Pods

Run:

```bash
kubectl get pods
```

Expected:

```text
NAME                    STATUS
mario-xxxx              Running
mario-yyyy              Running
```

---

# Verify Service

Run:

```bash
kubectl get services
```

Example:

```text
NAME             TYPE          EXTERNAL-IP

mario-service    LoadBalancer  xxxxx.elb.amazonaws.com
```

---

## Wait for External IP

AWS will provision the Load Balancer automatically.

Estimated time:

```text
~2 minutes
```

---

# Step 5 — Play Mario!

Get the service information:

```bash
kubectl get service mario-service
```

Copy:

```text
EXTERNAL-IP
```

---

Open browser:

```text
http://<EXTERNAL_IP>
```

Expected result:

```text
🎮 Super Mario runs on Kubernetes on AWS!
```

---

# Application Access Flow

```text
Browser

   │

   ▼

External AWS Load Balancer

   │

   ▼

Kubernetes Service

   │

   ▼

Mario Pod

   │

   ▼

Game Container
```

---

# Step 6 — Cleanup Resources

Remove Kubernetes Deployment:

```bash
kubectl delete -f mario-deployment.yaml
```

Remove Kubernetes Service:

```bash
kubectl delete -f mario-service.yaml
```

---

## Destroy Terraform Infrastructure

Run:

```bash
terraform destroy -auto-approve
```

---

# Cleanup Result

The following resources are deleted:

* ✅ EKS Cluster
* ✅ Worker Nodes
* ✅ Load Balancer
* ✅ Kubernetes Resources
* ✅ Supporting AWS Infrastructure

---

# Validation Checklist

Verify completion:

* ✅ Terraform initialized successfully
* ✅ EKS cluster created
* ✅ kubectl connected to EKS
* ✅ Mario deployment created
* ✅ Mario pods running
* ✅ LoadBalancer service created
* ✅ External IP assigned
* ✅ Game accessible from browser
* ✅ Resources cleaned after testing

---

# Production Best Practices

* 💡 Use private EKS worker nodes with controlled network access.
* 💡 Store container images in Amazon ECR instead of public repositories.
* 💡 Enable Kubernetes namespaces for workload isolation.
* 💡 Use Kubernetes Ingress with AWS Load Balancer Controller for advanced routing.
* 💡 Enable Amazon CloudWatch Container Insights for monitoring.
* 💡 Implement Kubernetes RBAC policies.
* 💡 Use Terraform remote state storage with Amazon S3 and DynamoDB locking.
* 💡 Scan container images for vulnerabilities before deployment.

---

# Troubleshooting

| Issue                       | Possible Cause               | Resolution                                            |
| --------------------------- | ---------------------------- | ----------------------------------------------------- |
| `kubectl get nodes` fails   | kubeconfig not configured    | Run `aws eks update-kubeconfig`.                      |
| Pods remain pending         | Worker nodes unavailable     | Check EKS node group status.                          |
| Image pull failure          | Container image unavailable  | Verify image name and registry access.                |
| External IP remains pending | Load Balancer creation delay | Wait and check AWS Load Balancer events.              |
| Application not loading     | Port mismatch                | Verify service port and container port configuration. |
