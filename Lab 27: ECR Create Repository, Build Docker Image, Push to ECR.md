# 1.12 AWS Container Services (ECR + ECS + EKS)

# Lab 27: ECR — Create Repository, Build Docker Image, Push to ECR

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will use **Amazon Elastic Container Registry (Amazon ECR)** to store and manage Docker container images.

You will create a private ECR repository, install Docker tools on an EC2 instance, authenticate Docker with Amazon ECR, build a Flask application container image, tag the image, and push it to ECR.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create a private Amazon ECR repository
* ✅ Install and configure Docker on an EC2 instance
* ✅ Configure AWS CLI credentials
* ✅ Authenticate Docker with Amazon ECR
* ✅ Build Docker container images
* ✅ Tag Docker images for ECR
* ✅ Push images to Amazon ECR
* ✅ Verify container images in the AWS Console

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Running EC2 instance
* SSH access to EC2 instance
* AWS CLI installed
* Docker installed
* IAM permissions for:

  * Amazon ECR
  * Amazon EC2

Required tools:

* Docker
* AWS CLI
* Git (optional)

---

# Architecture Overview

```text id="g8n3km"
                Developer / EC2 Instance
                         │
                         │
                         ▼
                  Docker Build
                         │
                         ▼
              Docker Image Created
              devops-flask-app
                         │
                         ▼
                 Amazon ECR
              Private Repository
                         │
                         ▼
             Container Image Storage
```

---

# Step 1 – Create ECR Repository

📍 **Navigation**

```text id="3h6k1q"
ECR → Repositories → Create repository
```

Configure the repository:

| Setting              | Value              |
| -------------------- | ------------------ |
| Visibility           | Private            |
| Repository Name      | `devops-flask-app` |
| Image Tag Mutability | Mutable            |

After completing the configuration:

* Click **Create repository**

> **Note**
>
> A private ECR repository stores container images securely and controls access through AWS IAM permissions.

---

# Step 2 – Install Docker on EC2 and Configure AWS CLI

SSH into the EC2 instance:

```bash id="v7q2ms"
ssh -i linux-key.pem ubuntu@<EC2_PUBLIC_IP>
```

---

## Install Required Packages

Update package information:

```bash id="4q9m7x"
sudo apt update
```

Install Docker and AWS CLI:

```bash id="m8p3jd"
sudo apt install docker.io awscli -y
```

---

## Add User to Docker Group

Run:

```bash id="2w6k9p"
sudo usermod -aG docker ubuntu
```

Apply the group changes:

```bash id="7j4m2x"
newgrp docker
```

Verify Docker installation:

```bash id="5s9k1d"
docker --version
```

---

## Configure AWS CLI Credentials

Run:

```bash id="x8m4qp"
aws configure
```

Enter:

```text id="j3k7mz"
Access Key:
Secret Key:
Default Region:
ap-south-1

Output format:
json
```

> **Important**
>
> Use an IAM user or role with appropriate ECR permissions.

---

# Step 3 – Authenticate Docker to ECR

Authenticate Docker with Amazon ECR.

Replace:

* `<ACCOUNT_ID>` with your AWS Account ID
* `REGION` with your AWS Region

Run:

```bash id="q2m7vx"
aws ecr get-login-password --region ap-south-1 \
| docker login --username AWS \
--password-stdin <ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com
```

Expected output:

```text id="w5p8nk"
Login Succeeded
```

> **Success**
>
> Docker is now authenticated and can push images to Amazon ECR.

---

# Step 4 – Build and Tag Docker Image

Navigate to your Flask application directory.

Build the Docker image:

```bash id="v8n2qx"
docker build -t devops-flask-app .
```

Verify the image:

```bash id="k6m9pt"
docker images
```

---

## Tag Docker Image for ECR

Replace:

* `<ACCOUNT_ID>` with your AWS Account ID

Run:

```bash id="p4z7my"
docker tag devops-flask-app:latest \
<ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/devops-flask-app:latest
```

Verify the tag:

```bash id="y3x8qw"
docker images
```

Expected output:

```text
<ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/devops-flask-app
```

---

# Step 5 – Push Image to ECR

Push the Docker image:

```bash id="r9m4ks"
docker push <ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/devops-flask-app:latest
```

Wait until the upload completes.

---

# Verify Image in AWS Console

📍 **Navigation**

```text id="m5q8vz"
ECR → Repositories → devops-flask-app → Images
```

Verify:

* Image name
* Image size
* Image digest
* Image tag

Expected result:

```text id="p2v7hx"
devops-flask-app:latest
```

> **Success**
>
> The Docker image has been successfully built and stored in Amazon ECR.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ ECR repository created
* ✅ Docker installed on EC2
* ✅ AWS CLI configured successfully
* ✅ Docker authenticated with ECR
* ✅ Flask Docker image built successfully
* ✅ Image tagged for ECR
* ✅ Image pushed to ECR
* ✅ Image visible in ECR repository

---

# Best Practices

* 💡 Use immutable image tags for production deployments instead of only using `latest`.
* 💡 Scan container images for vulnerabilities using Amazon ECR image scanning.
* 💡 Use IAM roles instead of storing AWS credentials on EC2 instances.
* 💡 Remove unused Docker images to reduce storage costs.
* 💡 Use automated CI/CD pipelines to build and push container images.
* 💡 Enable ECR lifecycle policies to automatically clean old images.

---

# Troubleshooting

| Issue                        | Possible Cause                      | Resolution                                               |
| ---------------------------- | ----------------------------------- | -------------------------------------------------------- |
| Docker login fails           | Incorrect AWS credentials or region | Verify AWS CLI configuration and ECR repository region.  |
| Push permission denied       | Missing ECR permissions             | Attach required ECR permissions to the IAM user or role. |
| Docker command requires sudo | User not added to Docker group      | Run `newgrp docker` or reconnect to the EC2 instance.    |
| Image push fails             | Incorrect repository URL            | Verify the ECR repository URI format.                    |
| Repository not found         | Incorrect repository name           | Confirm the repository name matches `devops-flask-app`.  |
