# SECTION 2 — Real-Time Projects

# Project 2: DevOps — End-to-End CI/CD Pipeline

> **Project Level:** 🔴 Expert
> **Pipeline Type:** Automated Container CI/CD Pipeline
> **Tools:** GitHub + AWS CodePipeline + AWS CodeBuild + Amazon ECR + ECS/EKS

---

# Overview

In this real-time project, you will build a complete **end-to-end CI/CD pipeline** for a containerized application.

The pipeline automates the complete software delivery lifecycle:

* Developer pushes code to GitHub
* AWS CodePipeline automatically triggers
* AWS CodeBuild runs automated tests
* Docker image is built
* Image is pushed to Amazon ECR
* Application is deployed to ECS or EKS
* Notifications are sent through Amazon SNS

This implementation follows modern DevOps practices with automated build, testing, containerization, and deployment.

---

# Learning Objectives

After completing this project, you will be able to:

* ✅ Integrate GitHub with AWS CodePipeline
* ✅ Configure automated CI/CD workflows
* ✅ Run application tests during the build phase
* ✅ Build Docker container images using CodeBuild
* ✅ Push Docker images to Amazon ECR
* ✅ Deploy applications automatically to ECS/EKS
* ✅ Configure deployment notifications using SNS

---

# Prerequisites

Before starting this project, ensure you have:

* AWS Account
* GitHub repository containing application source code
* Docker application with:

  * Dockerfile
  * Application source
  * Test cases
* Amazon ECR repository
* ECS cluster and service OR EKS cluster
* IAM permissions for:

  * CodePipeline
  * CodeBuild
  * ECR
  * ECS/EKS
  * SNS

---

# CI/CD Pipeline Architecture

```text
                         Developer

                            │

                            ▼

                         GitHub
                    (Source Repository)

                            │
                            │ Push Code
                            ▼

                    AWS CodePipeline

        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼

     Source              Build              Deploy

     GitHub            CodeBuild          ECS / EKS

                         │
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼

        Run Tests             Docker Build

                                      │

                                      ▼

                                  Amazon ECR

                                      │

                                      ▼

                               Container Deployment
```

---

# Step 1 — Pipeline Architecture

## Pipeline Workflow

The pipeline is triggered automatically when code is pushed to GitHub.

```text id="7m5xqp"
GitHub Push
     │
     ▼
Webhook Trigger
     │
     ▼
AWS CodePipeline
```

---

# Pipeline Stages

## Stage 1 — Source

**Provider:**

```text
GitHub
```

Actions:

* Detect repository changes
* Download source code
* Trigger pipeline execution

---

## Stage 2 — Build

**Provider:**

```text
AWS CodeBuild
```

Actions:

* Install dependencies
* Run automated tests
* Build Docker image
* Authenticate with Amazon ECR
* Push image to ECR

---

## Stage 3 — Deploy

Deployment options:

### ECS Deployment

```text
CodePipeline
        │
        ▼
ECS Cluster
        │
        ▼
ECS Service Update
```

### EKS Deployment

```text
CodePipeline
        │
        ▼
kubectl apply
        │
        ▼
Kubernetes Cluster
```

---

# Step 2 — Create buildspec.yml for Docker Build

Create the following file in the application repository:

```text
buildspec.yml
```

Add:

```yaml
version: 0.2

env:
  variables:
    AWS_ACCOUNT_ID: '<ACCOUNT_ID>'
    IMAGE_REPO: devops-flask-app
    REGION: ap-south-1

phases:

  pre_build:
    commands:
      - aws ecr get-login-password | docker login --username AWS --password-stdin
        $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com

      - IMAGE_TAG=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c1-7)

  build:
    commands:
      - docker build -t $IMAGE_REPO .
      - docker tag $IMAGE_REPO:latest
        $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com/$IMAGE_REPO:$IMAGE_TAG

  post_build:
    commands:
      - docker push
        $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com/$IMAGE_REPO:$IMAGE_TAG

      - printf '[{"name":"flask-container","imageUri":"%s"}]'
        $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com/$IMAGE_REPO:$IMAGE_TAG >
        imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
```

---

# Build Process Explanation

## Pre-Build Phase

Actions:

* Authenticate Docker with Amazon ECR
* Generate unique image tag using Git commit ID

Example:

```text
Commit:
a8f73bd9c8

Image Tag:
a8f73bd
```

---

## Build Phase

Actions:

```text
Source Code
      │
      ▼
Docker Build
      │
      ▼
Container Image
```

Command:

```bash
docker build -t $IMAGE_REPO .
```

---

## Post-Build Phase

Actions:

* Push Docker image to ECR
* Generate deployment definition file

Output:

```text
imagedefinitions.json
```

Example:

```json
[
 {
   "name":"flask-container",
   "imageUri":"ECR_IMAGE_URI"
 }
]
```

---

# Step 3 — Create CodePipeline

📍 **Navigation**

```text
CodePipeline → Create Pipeline
```

---

# Configure Source Stage

Provider:

```text
GitHub
```

Configuration:

| Setting    | Value                  |
| ---------- | ---------------------- |
| Repository | Your GitHub repository |
| Branch     | main                   |

Connect your GitHub account.

---

# Configure Build Stage

Provider:

```text
AWS CodeBuild
```

Select:

```text
Your CodeBuild Project
```

Example:

```text
devops-build
```

---

# Configure Deploy Stage

Select:

```text
Amazon ECS
```

Configuration:

| Setting | Value            |
| ------- | ---------------- |
| Cluster | `devops-cluster` |
| Service | `flask-service`  |

For EKS:

```bash
kubectl apply -f deployment.yaml
```

---

# Create Pipeline

Click:

```text
Create Pipeline
```

Expected workflow:

```text
GitHub
   ↓
CodeBuild
   ↓
ECR
   ↓
ECS/EKS
```

---

# Test Pipeline

Make a code change:

```bash
git add .
git commit -m "Update application"
git push
```

Monitor:

```text
CodePipeline Dashboard
```

Expected result:

```text
Source   ✅
Build    ✅
Deploy   ✅
```

---

# Step 4 — Add SNS Notification

Configure deployment notifications.

📍 **Navigation**

```text
CodePipeline → Notify → Create notification rule
```

---

# Notification Configuration

## Events

Select:

```text
Pipeline execution started
Pipeline execution succeeded
Pipeline execution failed
```

---

## Target

Select:

```text
SNS Topic
```

Configure:

```text
Destination:
Your email
```

Create the notification rule.

---

# Notification Workflow

```text
Pipeline Event

      │

      ▼

Amazon SNS

      │

      ▼

Email Notification
```

---

# Validation Checklist

Verify the following:

* ✅ GitHub repository connected
* ✅ CodePipeline created
* ✅ CodeBuild project configured
* ✅ Automated tests executed
* ✅ Docker image successfully built
* ✅ Image pushed to ECR
* ✅ ECS/EKS deployment completed
* ✅ SNS notifications configured
* ✅ Email notifications received

---

# Production Best Practices

* 💡 Use Git branching strategies such as GitFlow or trunk-based development.
* 💡 Add automated security scanning for container images.
* 💡 Store secrets in AWS Secrets Manager instead of source code.
* 💡 Use immutable Docker image tags instead of `latest`.
* 💡 Add manual approval stages before production deployment.
* 💡 Enable CloudWatch logging for pipeline troubleshooting.
* 💡 Use blue-green or canary deployment strategies for production workloads.
* 💡 Implement Infrastructure as Code using AWS CloudFormation or Terraform.

---

# Troubleshooting

| Issue                     | Possible Cause               | Resolution                                                       |
| ------------------------- | ---------------------------- | ---------------------------------------------------------------- |
| Pipeline does not trigger | GitHub webhook issue         | Reconnect GitHub source provider and verify webhook permissions. |
| CodeBuild fails           | Incorrect buildspec.yml      | Validate YAML syntax and build commands.                         |
| Docker push fails         | Missing ECR permissions      | Verify CodeBuild IAM role has ECR access.                        |
| ECS deployment fails      | Invalid image definition     | Check `imagedefinitions.json` configuration.                     |
| No SNS email received     | Subscription not confirmed   | Confirm SNS email subscription.                                  |
| EKS deployment fails      | Kubernetes permissions issue | Verify IAM and kubectl access configuration.                     |
