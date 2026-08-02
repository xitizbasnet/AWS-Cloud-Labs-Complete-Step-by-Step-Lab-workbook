# 1.3 AWS Storage Services

# Lab 9: Amazon S3 Lifecycle Management

> **⏱ Duration:** 20 Minutes
> **📈 Difficulty:** Intermediate

---

## 📖 Overview

This lab demonstrates how to configure Amazon S3 Lifecycle Management rules to automatically transition objects between different S3 storage classes and expire objects after a defined period.

S3 Lifecycle rules help optimize storage costs by automatically moving infrequently accessed data to lower-cost storage classes and deleting objects that are no longer required.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create an Amazon S3 Lifecycle rule.
* Configure object transitions between S3 storage classes.
* Configure object expiration settings.
* Understand automated S3 storage cost optimization.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* An existing Amazon S3 bucket.
* Permission to manage S3 bucket lifecycle configurations.

> ℹ️ **Note**
>
> Lifecycle rules automatically apply actions to objects based on their age and configured conditions.

---

# Step 1 – Create an S3 Lifecycle Rule

## 🔄 Configure Lifecycle Management

Navigate to:

**Amazon S3 → Your Bucket → Management**

Perform the following:

1. Select **Lifecycle rules**.
2. Click **Create lifecycle rule**.

Configure the rule:

| Setting    | Value                |
| ---------- | -------------------- |
| Rule Name  | `archive-old-files`  |
| Rule Scope | Apply to all objects |

After configuration:

* Click **Next**.

---

# Step 2 – Configure Object Transitions

## 📦 Move Objects Between Storage Classes

Under:

**Lifecycle rule actions**

Select:

* ✅ **Transition current versions between storage classes**

Configure the following transitions:

### Transition 1

| Setting                    | Value          |
| -------------------------- | -------------- |
| Storage Class              | S3 Standard-IA |
| Days After Object Creation | 30             |

---

### Transition 2

Add another transition:

| Setting                    | Value                         |
| -------------------------- | ----------------------------- |
| Storage Class              | S3 Glacier Flexible Retrieval |
| Days After Object Creation | 90                            |

---

> 💡 **Learning Objective**
>
> Storage class transitions help reduce storage costs by moving data to more cost-effective storage tiers based on access patterns.

---

# Step 3 – Configure Object Expiration

## 🗑️ Automatically Delete Old Objects

Enable the following lifecycle actions:

### Expire Current Object Versions

Select:

* ✅ **Expire current versions of objects**

Configure:

| Setting                    | Value |
| -------------------------- | ----- |
| Days After Object Creation | 365   |

---

### Delete Unnecessary Objects

Select:

* ✅ **Delete expired object delete markers or incomplete multipart uploads**

After completing the configuration:

* Click **Create rule**.

---

# Step 4 – Review Lifecycle Rule Configuration

## 📊 Lifecycle Rule Summary

The configured lifecycle workflow:

| Timeline | Action        | Storage Class            |
| -------- | ------------- | ------------------------ |
| Day 0    | Upload Object | S3 Standard              |
| Day 30   | Move Object   | S3 Standard-IA (cheaper) |
| Day 90   | Move Object   | S3 Glacier (archive)     |
| Day 365  | Delete Object | Expire                   |

---

## 📝 Note

> Amazon S3 Lifecycle Management helps automatically control storage costs by moving data through different storage classes based on how frequently the data is accessed.

---

## 💡 Best Practices

* Use S3 Standard for frequently accessed data.
* Use S3 Standard-IA for infrequently accessed data that still requires quick retrieval.
* Use S3 Glacier storage classes for long-term archival data.
* Review lifecycle rules regularly to ensure they match business requirements.
* Avoid deleting data automatically unless retention requirements are confirmed.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created an Amazon S3 Lifecycle rule.
* Configured storage class transitions.
* Moved objects from S3 Standard to S3 Standard-IA.
* Moved objects from S3 Standard-IA to S3 Glacier Flexible Retrieval.
* Configured automatic object expiration after 365 days.

> 🎯 **Outcome**
>
> You have successfully configured Amazon S3 Lifecycle Management to automate storage optimization, reduce costs, and manage object retention automatically.
