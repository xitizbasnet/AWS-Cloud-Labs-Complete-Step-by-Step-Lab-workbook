# 1.3 AWS Storage Services

# Lab 12: Create and Mount Amazon Elastic File System (EFS) on EC2

> **⏱ Duration:** 30 Minutes
> **📈 Difficulty:** Intermediate

---

## 📖 Overview

This lab demonstrates how to create an Amazon Elastic File System (EFS), configure network access using security groups, mount the shared file system on an EC2 Linux instance, and verify shared storage access across multiple EC2 instances.

Amazon EFS provides scalable, elastic file storage that can be shared simultaneously across multiple EC2 instances.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create an Amazon EFS file system.
* Configure security group rules for NFS access.
* Mount EFS on a Linux EC2 instance.
* Verify shared storage between multiple EC2 instances.
* Configure persistent EFS mounting after system reboot.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* A Linux EC2 instance.
* SSH access to the EC2 instance.
* EC2 security group information.

> ℹ️ **Note**
>
> Amazon EFS uses the **Network File System (NFS)** protocol. Port **2049** must be allowed between EC2 instances and the EFS mount targets.

---

# Step 1 – Create an EFS File System

## 📁 Create Amazon EFS Storage

Navigate to:

**AWS Console → Search → EFS → Create file system**

Configure the following:

| Setting | Value        |
| ------- | ------------ |
| Name    | `devops-efs` |
| VPC     | Default VPC  |

After configuration:

* Click **Create** (Quick Create).
* Wait until the file system state becomes:

```text id="h8g2mw"
Available
```

> 💡 **Learning Objective**
>
> Amazon EFS automatically grows and shrinks as files are added or removed, eliminating the need to provision storage capacity manually.

---

# Step 2 – Configure Security Group for NFS

## 🔒 Allow EFS Network Access

Navigate to:

**EFS → Your File System → Network tab**

For each Availability Zone:

1. Click the associated Security Group.
2. Add an inbound rule.

Configure the rule:

| Setting | Value                               |
| ------- | ----------------------------------- |
| Type    | NFS                                 |
| Port    | 2049                                |
| Source  | Security group of your EC2 instance |

After completing the configuration:

* Save rules.

> ⚠️ **Important**
>
> Do not allow NFS access from the entire internet. Restrict access to the EC2 security group only.

---

# Step 3 – Mount EFS on EC2

## 🔐 Connect to the EC2 Instance

SSH into your Linux EC2 instance:

```bash id="6r4p9w"
ssh -i linux-key.pem ubuntu@<EC2_IP>
```

---

## 📦 Install NFS Client

Update package information:

```bash id="5xj2cd"
sudo apt update
```

Install the NFS client package:

```bash id="8v3n1m"
sudo apt install nfs-common -y
```

---

## 📁 Create Mount Point

Create a directory for EFS:

```bash id="q7m5ys"
sudo mkdir /efs
```

---

## 🔗 Mount the EFS File System

Retrieve the EFS DNS name from the AWS Console.

Example format:

```text id="f2r9nk"
fs-xxxxx.efs.ap-south-1.amazonaws.com
```

Mount the EFS file system:

```bash id="w6k3vb"
sudo mount -t nfs4 -o nfsvers=4.1 \
<EFS_DNS>:/ /efs
```

---

## ✅ Verify the Mount

Run:

```bash id="r4x8hm"
df -h | grep efs
```

Expected result:

* EFS appears mounted at `/efs`.

---

# Step 4 – Test Shared Storage

## 🔄 Verify Multi-Instance File Sharing

### Write Data from EC2-1

On the first EC2 instance:

```bash id="m8z1kc"
sudo sh -c 'echo "Shared file from EC2-1" > /efs/shared.txt'
```

---

### Mount the Same EFS on EC2-2

On another EC2 instance:

* Mount the same EFS file system.
* Access the shared directory.

Read the file:

```bash id="w9q2lf"
cat /efs/shared.txt
```

Expected output:

```text id="d4v8pk"
Shared file from EC2-1
```

> 🎉 **Success**
>
> Both EC2 instances can access the same file. This demonstrates the shared storage capability of Amazon EFS.

---

# Step 5 – Make EFS Mount Persistent

## 🔄 Configure Automatic Mounting

To ensure EFS remains mounted after reboot, edit the `/etc/fstab` file.

Open the file:

```bash id="k5m8ds"
sudo nano /etc/fstab
```

Add the following configuration:

```text id="n7x4vq"
<EFS_DNS>:/ /efs nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0
```

---

> 💡 **Tip**
>
> The `_netdev` option ensures the system waits for network availability before attempting to mount EFS during boot.

---

## 💡 Best Practices

* Restrict NFS access using security groups.
* Use EFS for shared application data across multiple EC2 instances.
* Use encryption for production EFS file systems.
* Monitor EFS performance and throughput requirements.
* Configure backups for important shared data.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created an Amazon EFS file system.
* Configured NFS security group access.
* Installed the NFS client on Linux EC2.
* Mounted EFS on an EC2 instance.
* Verified shared file access between multiple EC2 instances.
* Configured persistent EFS mounting.

> 🎯 **Outcome**
>
> You have successfully deployed and configured Amazon EFS as shared file storage for EC2 instances. This provides the foundation for scalable applications requiring shared access to files across multiple servers.
