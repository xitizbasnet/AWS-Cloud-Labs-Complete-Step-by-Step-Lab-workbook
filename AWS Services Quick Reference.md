# AWS Services Quick Reference

> **Purpose:** Quick reference guide for commonly used AWS services, their primary purpose, and available Free Tier limits.
> **Usage:** Suitable for AWS learning labs, DevOps practice environments, and cloud architecture planning.

---

# AWS Service Reference Table

| AWS Service                      | Purpose                        | Free Tier Limit                                 |
| -------------------------------- | ------------------------------ | ----------------------------------------------- |
| 🖥️ **Amazon EC2 (t2.micro)**    | Virtual Machines               | 750 hours/month                                 |
| 🗄️ **Amazon S3**                | Object Storage                 | 5 GB + 20K GET requests + 2K PUT requests/month |
| 🛢️ **Amazon RDS (db.t3.micro)** | Managed Databases              | 750 hours + 20 GB/month                         |
| ⚡ **AWS Lambda**                 | Serverless Functions           | 1M requests + 400K GB-seconds/month             |
| 📊 **Amazon DynamoDB**           | NoSQL Database                 | 25 GB + 25 RCU + 25 WCU                         |
| 🌍 **Amazon CloudFront**         | Content Delivery Network (CDN) | 50 GB data transfer/month                       |
| 📢 **Amazon SNS**                | Pub/Sub Notifications          | 1M publishes/month                              |
| 📬 **Amazon SQS**                | Message Queue Service          | 1M requests/month                               |
| 📈 **Amazon CloudWatch**         | Monitoring & Logs              | 10 metrics + 5 GB logs/month                    |
| 🔄 **AWS CodeBuild**             | CI/CD Build Service            | 100 build-minutes/month                         |
| 🐳 **Amazon ECR**                | Container Registry             | 500 MB private storage/month                    |
| 🔐 **AWS IAM**                   | Identity & Access Management   | Always free                                     |
| 🌐 **Amazon VPC**                | Virtual Network Infrastructure | Always free *(data transfer charges apply)*     |
| 📡 **Amazon Route 53**           | DNS Service                    | `$0.50` per hosted zone/month *(NOT free)*      |

---

# Service Category Overview

## 🖥️ Compute Services

| Service        | Use Case                                         |
| -------------- | ------------------------------------------------ |
| **Amazon EC2** | Run virtual servers, applications, and workloads |
| **AWS Lambda** | Execute code without managing servers            |

---

## 🗄️ Storage & Database Services

| Service             | Use Case                                           |
| ------------------- | -------------------------------------------------- |
| **Amazon S3**       | Store files, backups, static websites, and objects |
| **Amazon RDS**      | Managed relational databases such as MySQL         |
| **Amazon DynamoDB** | High-performance NoSQL applications                |

---

## 🌐 Networking Services

| Service               | Use Case                                      |
| --------------------- | --------------------------------------------- |
| **Amazon VPC**        | Create isolated cloud networks                |
| **Amazon Route 53**   | Domain registration and DNS routing           |
| **Amazon CloudFront** | Deliver content globally using edge locations |

---

## 🔐 Security & Identity Services

| Service     | Use Case                             |
| ----------- | ------------------------------------ |
| **AWS IAM** | Manage users, roles, and permissions |

---

## 📊 Monitoring Services

| Service               | Use Case                                           |
| --------------------- | -------------------------------------------------- |
| **Amazon CloudWatch** | Monitor resources, applications, metrics, and logs |

---

## 🔄 DevOps & CI/CD Services

| Service           | Use Case                                   |
| ----------------- | ------------------------------------------ |
| **AWS CodeBuild** | Compile code, run tests, and create builds |
| **Amazon ECR**    | Store and manage Docker container images   |
| **Amazon SNS**    | Send notifications and event messages      |
| **Amazon SQS**    | Decouple applications using message queues |

---

# Free Tier Notes

> ⚠️ **Important:** Free Tier availability may vary depending on AWS account type, region, and current AWS Free Tier policies.

## Always Free Services

The following services include always-free usage options:

* ✅ AWS IAM
* ✅ Amazon VPC *(service itself is free; data transfer and related resources may incur charges)*

---

## Services with Potential Charges

The following services may generate costs if usage exceeds Free Tier limits:

* ⚠️ Amazon Route 53

  * Hosted zones are charged at approximately `$0.50/month`
* ⚠️ Amazon EC2

  * Additional storage, public IPv4 addresses, and data transfer may incur charges
* ⚠️ Amazon RDS

  * Storage and backup usage beyond limits may incur charges
* ⚠️ CloudFront

  * Data transfer beyond Free Tier allowance may incur charges

---

# Quick Service Selection Guide

| Requirement               | Recommended AWS Service    |
| ------------------------- | -------------------------- |
| Host a website            | S3 + CloudFront + Route 53 |
| Run an application server | EC2                        |
| Run code without servers  | Lambda                     |
| Store files               | S3                         |
| Store relational data     | RDS                        |
| Store NoSQL data          | DynamoDB                   |
| Build Docker images       | ECR + CodeBuild            |
| Deploy applications       | CodePipeline + ECS/EKS     |
| Monitor resources         | CloudWatch                 |
| Manage permissions        | IAM                        |
| Connect cloud networks    | VPC                        |

---

# AWS Learning Lab Recommendation

For beginners and DevOps learners, the recommended learning order is:

```text
IAM
 ↓
VPC
 ↓
EC2
 ↓
S3
 ↓
RDS
 ↓
Lambda
 ↓
CloudWatch
 ↓
CodeBuild / CodePipeline
 ↓
ECR + ECS/EKS
```

---

> **Reference Note:** Always verify current AWS Free Tier limits before deploying production workloads, as AWS pricing and Free Tier offerings may change over time.
