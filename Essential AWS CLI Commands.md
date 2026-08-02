# Essential AWS CLI Commands

> **Purpose:** Quick reference guide for commonly used AWS Command Line Interface (CLI) commands for AWS administration, automation, and DevOps workflows.

> **Prerequisites:**
>
> * AWS CLI installed and configured
> * IAM user/role with required permissions
> * Valid AWS credentials available

---

# AWS CLI Configuration

## Configure AWS Credentials

Configure AWS CLI with access credentials:

```bash
aws configure
```

You will be prompted for:

```text
AWS Access Key ID
AWS Secret Access Key
Default Region Name
Default Output Format
```

---

## View Current AWS CLI Configuration

Display current configuration:

```bash
aws configure list
```

---

## Check Current AWS Identity

Verify the active AWS account and IAM identity:

```bash
aws sts get-caller-identity
```

Example output:

```json
{
    "UserId": "AIDAXXXXXXXXX",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/admin"
}
```

---

# Amazon EC2 Commands

## List EC2 Instances

Display EC2 instances in a region:

```bash
aws ec2 describe-instances \
--region ap-south-1
```

**Purpose:**

* View instance details
* Check instance state
* Retrieve instance IDs

---

## Start an EC2 Instance

Start a stopped instance:

```bash
aws ec2 start-instances \
--instance-ids i-xxxxx
```

Replace:

```text
i-xxxxx
```

with your actual EC2 instance ID.

---

## Stop an EC2 Instance

Stop a running instance:

```bash
aws ec2 stop-instances \
--instance-ids i-xxxxx
```

---

# Amazon S3 Commands

## List S3 Buckets

Display all S3 buckets:

```bash
aws s3 ls
```

---

## Upload a File to S3

Copy a file to an S3 bucket:

```bash
aws s3 cp file.txt s3://my-bucket/
```

Example:

```bash
aws s3 cp application.zip s3://devops-backups/
```

---

## Synchronize a Folder with S3

Upload and synchronize a local directory:

```bash
aws s3 sync ./folder s3://my-bucket/folder/
```

**Use cases:**

* Website deployment
* Backup automation
* Data synchronization

---

# Amazon EKS Commands

## Configure kubectl for EKS Cluster

Update Kubernetes configuration:

```bash
aws eks update-kubeconfig \
--name cluster \
--region ap-south-1
```

Replace:

```text
cluster
```

with your EKS cluster name.

---

## Verify Kubernetes Cluster

Check nodes and running workloads:

```bash
kubectl get nodes && kubectl get pods
```

Equivalent commands:

```bash
kubectl get nodes
```

```bash
kubectl get pods
```

---

# Amazon ECR Commands

## Authenticate Docker with Amazon ECR

Generate an authentication token and log in:

```bash
aws ecr get-login-password \
| docker login \
--username AWS \
--password-stdin \
<ACCOUNT>.dkr.ecr.ap-south-1.amazonaws.com
```

Replace:

```text
<ACCOUNT>
```

with your AWS account ID.

---

# Common AWS CLI Workflow Examples

## EC2 Management Workflow

```text
Configure AWS CLI
        │
        ▼
Check Identity
        │
        ▼
List Instances
        │
        ▼
Start / Stop Instances
```

Commands:

```bash
aws configure

aws sts get-caller-identity

aws ec2 describe-instances \
--region ap-south-1

aws ec2 start-instances \
--instance-ids i-xxxxx
```

---

## S3 Deployment Workflow

```text
Local Files

     │

     ▼

AWS CLI Sync

     │

     ▼

Amazon S3 Bucket
```

Command:

```bash
aws s3 sync ./website s3://my-bucket/
```

---

## Container Deployment Workflow

```text
Docker Image

      │

      ▼

Amazon ECR

      │

      ▼

Amazon EKS

      │

      ▼

Kubernetes Pods
```

Commands:

```bash
aws ecr get-login-password | docker login
```

```bash
docker push <ECR_IMAGE_URI>
```

```bash
aws eks update-kubeconfig \
--name cluster \
--region ap-south-1
```

---

# AWS CLI Best Practices

* ✅ Use IAM roles instead of storing long-term credentials where possible.
* ✅ Configure named profiles for multiple AWS accounts.
* ✅ Verify the active account before running destructive commands.
* ✅ Use least-privilege IAM permissions.
* ✅ Enable MFA for administrative accounts.
* ✅ Avoid storing AWS access keys in scripts or repositories.
* ✅ Use AWS Systems Manager Session Manager instead of SSH where possible.

---

# Quick Command Reference

| Service           | Command                       | Purpose                     |
| ----------------- | ----------------------------- | --------------------------- |
| AWS Configuration | `aws configure`               | Configure credentials       |
| Identity          | `aws sts get-caller-identity` | Check current AWS identity  |
| EC2               | `aws ec2 describe-instances`  | List instances              |
| EC2               | `aws ec2 start-instances`     | Start instances             |
| EC2               | `aws ec2 stop-instances`      | Stop instances              |
| S3                | `aws s3 ls`                   | List buckets                |
| S3                | `aws s3 cp`                   | Upload files                |
| S3                | `aws s3 sync`                 | Synchronize folders         |
| EKS               | `aws eks update-kubeconfig`   | Configure Kubernetes access |
| ECR               | `aws ecr get-login-password`  | Authenticate Docker         |
