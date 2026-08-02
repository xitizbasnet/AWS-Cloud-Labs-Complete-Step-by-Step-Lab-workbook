# 1.3 AWS Storage Services

# Lab 8: Amazon S3 Cross-Region Replication (CRR)

> **⏱ Duration:** 20 Minutes
> **📈 Difficulty:** Intermediate

---

## 📖 Overview

This lab demonstrates how to configure **Amazon S3 Cross-Region Replication (CRR)** to automatically replicate objects from a source bucket in one AWS Region to a destination bucket in another Region. Cross-Region Replication helps improve data redundancy, disaster recovery, and compliance.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create a destination Amazon S3 bucket in a different AWS Region.
* Enable bucket versioning on both source and destination buckets.
* Configure an Amazon S3 Cross-Region Replication (CRR) rule.
* Verify that objects are automatically replicated across Regions.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* The source bucket created in **Lab 7**.
* Permissions to create IAM roles and Amazon S3 buckets.

> ⚠️ **Important**
>
> Amazon S3 **Versioning must be enabled** on both the source and destination buckets before Cross-Region Replication can be configured.

---

# Step 1 – Create the Destination Bucket

## 🪣 Create a Replica Bucket

Navigate to:

**Amazon S3 → Create bucket**

Configure the following settings:

| Setting     | Value                                            |
| ----------- | ------------------------------------------------ |
| Bucket Name | `vinod-devops-replica-2024`                      |
| AWS Region  | `us-east-1` *(different from the source bucket)* |

Keep all remaining settings at their default values.

After completing the configuration:

* Click **Create bucket**.

> ℹ️ **Note**
>
> The destination bucket must reside in a **different AWS Region** to support Cross-Region Replication (CRR).

---

# Step 2 – Enable Bucket Versioning

## 🔄 Configure Versioning on Both Buckets

### Source Bucket (`ap-south-1`)

Navigate to:

**Source Bucket → Properties → Bucket Versioning → Edit**

Configure the following:

* Enable **Bucket Versioning**.
* Click **Save**.

---

### Destination Bucket (`us-east-1`)

Navigate to:

**Destination Bucket → Properties → Bucket Versioning → Edit**

Configure the following:

* Enable **Bucket Versioning**.
* Click **Save**.

> ⚠️ **Required**
>
> Bucket Versioning is **mandatory** for Amazon S3 Cross-Region Replication.

---

# Step 3 – Create a Replication Rule

## 🌍 Configure Cross-Region Replication

Navigate to:

**Source Bucket → Management → Replication Rules → Create Rule**

Configure the following settings:

| Setting     | Value                                        |
| ----------- | -------------------------------------------- |
| Rule Name   | `replicate-to-us-east`                       |
| Status      | Enabled                                      |
| Source      | Apply to all objects in bucket               |
| Destination | Browse Amazon S3 → Select the replica bucket |
| IAM Role    | Create new role *(automatically created)*    |

After reviewing the configuration:

* Click **Save**.

> 💡 **Tip**
>
> AWS automatically creates an IAM role that grants Amazon S3 permission to replicate objects between buckets.

---

# Step 4 – Test Replication

## ✅ Verify Cross-Region Replication

Perform the following steps:

1. Upload a new file to the **source bucket** in **`ap-south-1`**.
2. Wait approximately **1–2 minutes**.
3. Open the **destination bucket** in **`us-east-1`**.
4. Confirm that the uploaded file appears in the replica bucket.

> 🎉 **Success**
>
> If the uploaded object is visible in the destination bucket, Amazon S3 Cross-Region Replication has been configured successfully.

---

## 📝 Note

> Cross-Region Replication only replicates **new objects** uploaded after the replication rule is created. Existing objects are **not replicated automatically** unless additional replication options are configured.

---

## 💡 Best Practices

* Enable **Versioning** before configuring replication.
* Use Cross-Region Replication for disaster recovery and business continuity.
* Monitor replication status using Amazon S3 object metadata.
* Apply least-privilege IAM permissions for replication roles.
* Consider lifecycle policies to manage storage costs in replicated buckets.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created a destination Amazon S3 bucket in a different AWS Region.
* Enabled bucket versioning on both source and destination buckets.
* Configured an Amazon S3 Cross-Region Replication (CRR) rule.
* Verified automatic replication of newly uploaded objects.

> 🎯 **Outcome**
>
> You have successfully configured Amazon S3 Cross-Region Replication, enabling automatic object replication between AWS Regions for improved durability, disaster recovery, and data availability.
