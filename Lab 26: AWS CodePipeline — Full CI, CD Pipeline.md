# 1.11 CI/CD Tools and Pipeline

# Lab 26: AWS CodePipeline — Full CI/CD Pipeline

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 60 Minutes

---

# Overview

In this lab, you will build a complete **Continuous Integration and Continuous Deployment (CI/CD) pipeline** using AWS developer tools:

* **AWS CodeCommit** — Source code repository
* **AWS CodeBuild** — Application build and testing
* **AWS CodeDeploy** — Application deployment
* **AWS CodePipeline** — Pipeline orchestration

The completed pipeline automatically builds, tests, and deploys application changes whenever new code is pushed to the repository.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create and use an AWS CodeCommit repository
* ✅ Configure AWS CodeBuild projects
* ✅ Create build specifications using `buildspec.yml`
* ✅ Configure AWS CodeDeploy applications and deployment groups
* ✅ Define deployment instructions using `appspec.yml`
* ✅ Create a complete AWS CodePipeline workflow
* ✅ Automate application deployment to EC2 instances

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Git installed and configured
* Application source code
* EC2 deployment target:

  * Running EC2 instance
  * CodeDeploy Agent installed
  * Instance tagged correctly
* IAM permissions for:

  * AWS CodeCommit
  * AWS CodeBuild
  * AWS CodeDeploy
  * AWS CodePipeline

---

# Architecture Overview

```text id="r5p8c1"
                  Developer
                      │
                      │ git push
                      ▼
              AWS CodeCommit
              (Source Repository)
                      │
                      ▼
              AWS CodePipeline
                      │
        ┌─────────────┴─────────────┐
        │                           │
        ▼                           ▼

   AWS CodeBuild              AWS CodeDeploy
   Build + Test               Deployment

                                      │
                                      ▼

                              EC2 Application Server
```

---

# Step 1 – Push Application to CodeCommit

📍 **Navigation**

```text id="q8x4mn"
CodeCommit → Create repository
```

Create the repository:

| Setting         | Value        |
| --------------- | ------------ |
| Repository Name | `devops-app` |

After creating the repository:

1. Copy the HTTPS clone URL.
2. Clone the repository.

```bash id="w1k8s0"
git clone <url>
```

Copy your application files into the repository.

Commit and push the application:

```bash id="2j7k8n"
git add .
git commit -m "Initial"
git push
```

> **Success**
>
> The application source code is now stored in AWS CodeCommit.

---

# Step 2 – Create CodeBuild Project

📍 **Navigation**

```text id="6m9v2p"
CodeBuild → Build projects → Create project
```

Configure the build project:

| Setting          | Value                        |
| ---------------- | ---------------------------- |
| Project Name     | `devops-build`               |
| Source Provider  | CodeCommit                   |
| Repository       | `devops-app`                 |
| Environment Type | Managed image                |
| Operating System | Ubuntu                       |
| Image            | `aws/codebuild/standard:7.0` |
| Buildspec        | Use buildspec file           |

Create the build project.

---

# Step 3 – Create buildspec.yml in Repository

Create the following file in the repository root:

```text id="7q5n2x"
buildspec.yml
```

Add:

```yaml id="9k2f7v"
version: 0.2

phases:

  install:
    commands:
      - echo Installing dependencies
      - pip install -r requirements.txt

  build:
    commands:
      - echo Build started
      - python -m pytest tests/ || true

  post_build:
    commands:
      - echo Build complete

artifacts:
  files:
    - '**/*'
```

Commit and push the file:

```bash id="1m4q7s"
git add buildspec.yml
git commit -m "Add buildspec"
git push
```

> **Note**
>
> The `buildspec.yml` file defines the commands CodeBuild executes during the build process.

---

# Step 4 – Create CodeDeploy Application

📍 **Navigation**

```text id="m6w2k9"
CodeDeploy → Applications → Create application
```

Configure:

| Setting          | Value           |
| ---------------- | --------------- |
| Application Name | `devops-deploy` |
| Compute Platform | EC2/On-premises |

Create the application.

---

## Create Deployment Group

Configure:

| Setting               | Value                                 |
| --------------------- | ------------------------------------- |
| Deployment Group Name | `devops-dg`                           |
| Service Role          | CodeDeploy role (`AWSCodeDeployRole`) |
| Deployment Type       | In-place                              |
| EC2 Instance Tag      | Key: `Name`                           |
| Tag Value             | `app-server`                          |

Create the deployment group.

> **Note**
>
> CodeDeploy uses EC2 instance tags to identify deployment targets.

---

# Step 5 – Create appspec.yml in Repository

Create:

```text id="5r8j2p"
appspec.yml
```

Add:

```yaml id="x2p9km"
version: 0.0

os: linux

files:
  - source: /
    destination: /home/ubuntu/app

hooks:

  BeforeInstall:
    - location: scripts/install_deps.sh
      timeout: 300

  ApplicationStart:
    - location: scripts/start_app.sh
      timeout: 300
```

Commit and push:

```bash id="q7m4xd"
git add appspec.yml
git commit -m "Add CodeDeploy configuration"
git push
```

> **Note**
>
> The `appspec.yml` file defines how CodeDeploy installs and starts the application on target instances.

---

# Step 6 – Create CodePipeline

📍 **Navigation**

```text id="p8m1k4"
CodePipeline → Pipelines → Create pipeline
```

Configure pipeline:

| Stage            | Configuration     |
| ---------------- | ----------------- |
| Pipeline Name    | `devops-pipeline` |
| Source Stage     | CodeCommit        |
| Repository       | `devops-app`      |
| Branch           | `main`            |
| Build Stage      | CodeBuild         |
| Build Project    | `devops-build`    |
| Deploy Stage     | CodeDeploy        |
| Application      | `devops-deploy`   |
| Deployment Group | `devops-dg`       |

Create the pipeline.

Expected workflow:

```text
Source → Build → Deploy
```

> **Success**
>
> The CI/CD pipeline is now configured and will automatically execute when new changes are pushed.

---

# Step 7 – Test Full Pipeline

Make a code change in your application.

Commit and push:

```bash id="f4x9s7"
git add .
git commit -m "Update app"
git push
```

Monitor the pipeline:

```text id="z8m3q1"
CodePipeline Dashboard
```

Pipeline stages:

```text
Source  →  Build  →  Deploy
```

Expected result:

```text id="j4v8np"
All stages successful (green)
```

The updated application is deployed automatically to the EC2 instance.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ CodeCommit repository created
* ✅ Application pushed successfully
* ✅ CodeBuild project configured
* ✅ `buildspec.yml` created
* ✅ CodeDeploy application created
* ✅ Deployment group configured
* ✅ `appspec.yml` created
* ✅ CodePipeline created
* ✅ Code changes automatically deployed

---

# Best Practices

* 💡 Use separate branches for development, testing, and production releases.
* 💡 Add automated unit and integration tests in CodeBuild.
* 💡 Store application secrets in AWS Secrets Manager or Systems Manager Parameter Store.
* 💡 Use deployment approval stages for production environments.
* 💡 Enable CloudWatch monitoring for pipeline failures.
* 💡 Use immutable deployment strategies for critical workloads.

---

# Troubleshooting

| Issue                                 | Possible Cause                | Resolution                                                               |
| ------------------------------------- | ----------------------------- | ------------------------------------------------------------------------ |
| CodeCommit push fails                 | Authentication not configured | Configure Git credentials for AWS CodeCommit.                            |
| CodeBuild fails                       | Missing dependencies          | Verify `requirements.txt` and build commands.                            |
| Deployment fails                      | CodeDeploy Agent issue        | Confirm the CodeDeploy Agent is installed and running on EC2.            |
| EC2 instance not detected             | Incorrect tag configuration   | Verify EC2 tags match the deployment group settings.                     |
| Pipeline does not start automatically | Source trigger issue          | Confirm CodePipeline has access to the CodeCommit repository and branch. |
