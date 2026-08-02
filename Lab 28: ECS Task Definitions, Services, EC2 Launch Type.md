# 1.12 AWS Container Services (ECR + ECS + EKS)

# Lab 28: ECS — Task Definitions, Services, EC2 Launch Type

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 60 Minutes

---

# Overview

In this lab, you will deploy a containerized Flask application using **Amazon Elastic Container Service (Amazon ECS)** with the **EC2 launch type**.

You will create an ECS cluster, define a task definition using a Docker image stored in Amazon ECR, create an ECS service to run multiple containers, verify running tasks, and configure ECS Auto Scaling.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an Amazon ECS cluster using EC2 infrastructure
* ✅ Create ECS task definitions
* ✅ Deploy containers using ECS services
* ✅ Run Docker images from Amazon ECR
* ✅ Verify running ECS tasks
* ✅ Configure ECS Service Auto Scaling
* ✅ Automatically scale container workloads based on CPU utilization

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Completed **Lab 27: ECR — Create Repo, Build Docker Image, Push to ECR**
* Docker image available in Amazon ECR:

```text id="6s7m1p"
devops-flask-app:latest
```

* IAM permissions for:

  * Amazon ECS
  * Amazon EC2
  * Amazon ECR
  * Elastic Load Balancing (optional)

---

# Architecture Overview

```text id="r8v2mq"
                    Amazon ECS Cluster
                          │
                          │
                EC2 Container Instances
                          │
          ┌───────────────┴───────────────┐
          │                               │
          ▼                               ▼

     ECS Task 1                      ECS Task 2
   Flask Container                 Flask Container

          │                               │
          └───────────────┬───────────────┘
                          │
                          ▼

                 Amazon ECR Image
             devops-flask-app:latest
```

---

# Step 1 – Create ECS Cluster

📍 **Navigation**

```text id="7q4m8v"
ECS → Clusters → Create cluster
```

Configure the cluster:

| Setting           | Value                  |
| ----------------- | ---------------------- |
| Cluster Name      | `devops-cluster`       |
| Infrastructure    | Amazon EC2 instances   |
| EC2 Instance Type | `t2.micro`             |
| Desired Capacity  | Minimum: 1, Maximum: 2 |
| Operating System  | Amazon Linux 2         |

After completing the configuration:

* Click **Create cluster**

> **Note**
>
> The ECS cluster provides the infrastructure where ECS tasks and services will run.

---

# Step 2 – Create Task Definition

A task definition describes how ECS should run your container.

📍 **Navigation**

```text id="k8m2qp"
ECS → Task definitions → Create new
```

Configure:

| Setting     | Value        |
| ----------- | ------------ |
| Family Name | `flask-task` |
| Launch Type | EC2          |

---

## Container Configuration

Add a container:

| Setting        | Value                                                                |
| -------------- | -------------------------------------------------------------------- |
| Container Name | `flask-container`                                                    |
| Image URI      | `<ACCOUNT>.dkr.ecr.ap-south-1.amazonaws.com/devops-flask-app:latest` |
| Port Mapping   | `5000:5000`                                                          |
| Memory         | `256 MiB`                                                            |
| CPU            | `256`                                                                |

Create the task definition.

> **Note**
>
> The task definition acts as a blueprint for ECS containers, including image location, resources, networking, and runtime configuration.

---

# Step 3 – Create ECS Service

An ECS service ensures that the required number of tasks remain running.

📍 **Navigation**

```text id="h6q3mz"
ECS → Cluster → Services → Create
```

Configure the service:

| Setting         | Value           |
| --------------- | --------------- |
| Launch Type     | EC2             |
| Task Definition | `flask-task`    |
| Revision        | LATEST          |
| Service Name    | `flask-service` |
| Desired Tasks   | 2               |

---

## Load Balancer Configuration

Optional:

* Select an existing Application Load Balancer (ALB)

or:

* Skip load balancer configuration for this lab.

Create the service.

> **Success**
>
> ECS launches the required number of Flask containers based on the task definition.

---

# Step 4 – Verify Running Tasks

📍 **Navigation**

```text id="p5x9mv"
ECS → Cluster → Tasks
```

Verify:

```text id="m7n3xq"
Running Tasks: 2
```

Select a running task.

Record:

* EC2 instance IP address
* Container port

Test the Flask application:

```bash id="h4w8sq"
curl http://<EC2_IP>:5000
```

Expected result:

```text id="n8k2vp"
Your Flask app responds!
```

> **Success**
>
> The Flask application is successfully running inside ECS containers.

---

# Step 5 – ECS Auto Scaling

Configure automatic task scaling.

📍 **Navigation**

```text id="y6r9mt"
ECS Service → Auto Scaling → Create
```

Configure:

| Setting        | Value                           |
| -------------- | ------------------------------- |
| Minimum Tasks  | 1                               |
| Maximum Tasks  | 4                               |
| Scaling Policy | Target Tracking                 |
| Metric         | ECSServiceAverageCPUUtilization |
| Target Value   | 60%                             |

Create the scaling policy.

Expected behavior:

```text id="w3k7qp"
CPU increases → ECS adds tasks
CPU decreases → ECS removes tasks
```

> **Success**
>
> ECS automatically adjusts the number of running containers based on workload demand.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ ECS cluster created successfully
* ✅ EC2 container instances registered
* ✅ Task definition created
* ✅ ECR image configured in task definition
* ✅ ECS service created
* ✅ Two tasks running successfully
* ✅ Flask application accessible
* ✅ ECS Auto Scaling configured

---

# Best Practices

* 💡 Use Application Load Balancers for production ECS services.
* 💡 Store container images securely in Amazon ECR.
* 💡 Use task roles instead of embedding AWS credentials inside containers.
* 💡 Configure CloudWatch Container Insights for ECS monitoring.
* 💡 Use health checks to automatically replace unhealthy tasks.
* 💡 Use capacity providers to manage ECS infrastructure efficiently.
* 💡 Use immutable container image tags instead of relying on `latest` in production.

---

# Troubleshooting

| Issue                            | Possible Cause                  | Resolution                                                |
| -------------------------------- | ------------------------------- | --------------------------------------------------------- |
| ECS task fails to start          | Incorrect ECR image URI         | Verify the repository URL and image tag.                  |
| Task cannot pull image           | Missing ECR permissions         | Attach required ECR permissions to the ECS instance role. |
| Flask application is unreachable | Incorrect port mapping          | Confirm container port `5000` is mapped correctly.        |
| No running tasks                 | ECS service configuration issue | Review ECS service events for errors.                     |
| Auto Scaling does not trigger    | Incorrect scaling policy        | Verify CPU metric and target tracking configuration.      |
