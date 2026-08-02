# 1.2 AWS Identity & Access Management (IAM)

# Lab 5: Working with AWS IAM Users, Groups, Policies

> **⏱ Duration:** 45 Minutes
> **📈 Difficulty:** Intermediate

---

## 📖 Overview

This lab introduces the core components of AWS Identity and Access Management (IAM). You will create IAM users and groups, assign AWS managed policies, create a custom IAM policy, configure an IAM role for Amazon EC2, and validate least-privilege access.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create IAM users and groups.
* Attach AWS managed and custom IAM policies.
* Test least-privilege access.
* Create and use IAM roles for Amazon EC2.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* Root user access (required for IAM administration).
* An existing EC2 instance (used for attaching an IAM role).

> 🔒 **Security Best Practice**
>
> Perform daily administrative tasks using IAM users instead of the AWS root account. Reserve the root account for account-level management only.

---

# Step 1 – Navigate to AWS IAM

## 👤 Open the IAM Console

1. Search for **IAM** in the AWS Management Console.
2. Open the **IAM** service.

> ℹ️ **Note**
>
> IAM is a **Global** AWS service and is not associated with a specific AWS Region.

The IAM dashboard provides access to:

* Users
* Groups
* Roles
* Policies

---

# Step 2 – Create an IAM Group

## 👥 Create the User Group

Navigate to:

**User groups → Create group**

Configure the following settings:

| Setting    | Value              |
| ---------- | ------------------ |
| Group Name | `DevOps-Engineers` |

Attach the following AWS managed policies:

* Search **EC2** → **AmazonEC2FullAccess**
* Search **S3** → **AmazonS3FullAccess**

After selecting the policies:

* Click **Create group**.

---

# Step 3 – Create an IAM User

## 👤 Create the User Account

Navigate to:

**Users → Create user**

Configure the following settings:

| Setting                | Value                              |
| ---------------------- | ---------------------------------- |
| Username               | `vinod-devops`                     |
| Console Access         | Provide user access to AWS Console |
| User Type              | I want to create an IAM user       |
| Console Password       | Custom → `DevOps@2024`             |
| Require Password Reset | Unchecked                          |

After completing the configuration:

* Click **Next**.

> ⚠️ **Important**
>
> In production environments, use strong, unique passwords and enable Multi-Factor Authentication (MFA) instead of relying on static passwords.

---

# Step 4 – Assign the User to a Group

## ➕ Add Group Membership

On the **Permissions** page:

* Add user to group:

  * `DevOps-Engineers`

Then:

* Click **Next**.
* Click **Create user**.

> 💡 **Tip**
>
> Save the **AWS Console sign-in URL** displayed after user creation. It is required for IAM users to access the AWS Management Console.

---

# Step 5 – Create a Custom IAM Policy

## 📄 Create a Policy Using JSON

Navigate to:

**Policies → Create policy → JSON**

Paste the following policy, which grants **read-only access** to a specific Amazon S3 bucket.

---

# Step 6 – Custom Policy JSON

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-devops-bucket",
        "arn:aws:s3:::my-devops-bucket/*"
      ]
    }
  ]
}
```

Configure the policy:

| Setting     | Value                      |
| ----------- | -------------------------- |
| Policy Name | `S3-ReadOnly-DevOpsBucket` |

After reviewing the configuration:

* Click **Create policy**.

> 💡 **Learning Objective**
>
> Custom IAM policies allow administrators to grant fine-grained permissions following the principle of least privilege.

---

# Step 7 – Create an IAM Role for Amazon EC2

## 🛡️ Configure the EC2 Role

Navigate to:

**Roles → Create role**

Configure the following:

| Setting         | Value                |
| --------------- | -------------------- |
| Trusted Entity  | AWS service          |
| Use Case        | EC2                  |
| Attached Policy | AmazonS3FullAccess   |
| Role Name       | `EC2-S3-Access-Role` |

Complete the role creation by clicking:

* **Next**
* **Create role**

### Attach the Role to an EC2 Instance

Navigate to:

**EC2 → Select Instance → Actions → Security → Modify IAM Role**

Select:

```text
EC2-S3-Access-Role
```

Then click:

* **Update IAM Role**

> 🔒 **Best Practice**
>
> Use IAM roles instead of storing AWS access keys directly on EC2 instances.

---

# Step 8 – Test User Access

## ✅ Verify Least-Privilege Permissions

Perform the following validation:

1. Log out of the AWS root account.
2. Log in as:

```text
vinod-devops
```

using the saved AWS Console sign-in URL.

Test the following permissions:

| Test            | Expected Result                          |
| --------------- | ---------------------------------------- |
| Can access EC2? | ✅ Yes                                    |
| Can access IAM? | ❌ No (not included in assigned policies) |

> 📝 **Note**
>
> This demonstrates the **Principle of Least Privilege**, where users receive only the permissions required to perform their assigned tasks.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created an IAM user group.
* Attached AWS managed policies.
* Created an IAM user.
* Assigned the user to a group.
* Created a custom IAM policy using JSON.
* Created an IAM role for Amazon EC2.
* Attached the IAM role to an EC2 instance.
* Verified user permissions based on assigned policies.

> 🎯 **Outcome**
>
> You have successfully implemented fundamental AWS IAM concepts, including users, groups, managed policies, custom policies, IAM roles, and least-privilege access. These are essential building blocks for securing AWS environments and managing permissions effectively.
