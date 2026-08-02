# 1.1 AWS Basic Labs

# Lab 2: CloudWatch — Create Billing Alarm & Service Limits

> **⏱ Duration:** 20 Minutes
> **📈 Difficulty:** Beginner

---

## 📖 Overview

This lab demonstrates how to configure an AWS billing alarm using Amazon CloudWatch and review AWS service limits. Setting up billing alerts before creating AWS resources is a recommended best practice to help prevent unexpected charges.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Enable AWS billing alerts.
* Create a CloudWatch billing alarm.
* Configure Amazon SNS email notifications.
* Review AWS service limits using AWS Trusted Advisor.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS Free Tier account.
* Access to the AWS Management Console.
* A valid email address for receiving billing notifications.

> ⚠️ **Important**
>
> Billing metrics are available **only** in the **US East (N. Virginia) (`us-east-1`)** Region.

---

# Step 1 – Enable Billing Alerts

## 💰 Configure Billing Preferences

Navigate to the AWS Billing Dashboard.

1. In the AWS Console, click your **Account Name**.
2. Select **Billing Dashboard**.
3. From the left navigation pane, select **Billing Preferences**.
4. Check **Receive Billing Alerts**.
5. Click **Save Preferences**.

---

# Step 2 – Open Amazon CloudWatch

## 📊 Access the CloudWatch Console

1. Search for **CloudWatch** using the AWS Console search bar.
2. Open the **CloudWatch** console.
3. Change the AWS Region to:

```text
US East (N. Virginia) - us-east-1
```

> ⚠️ **Important**
>
> Billing metrics are only available in the **us-east-1** Region.

---

# Step 3 – Create a Billing Alarm

## 🚨 Select the Billing Metric

Navigate through the CloudWatch console:

1. From the left navigation pane, select:

   * **Alarms**
   * **Billing**
   * **Create Alarm**
2. Click **Select metric**.
3. Select the following metric:

   * **Billing**
   * **Total Estimated Charge**
   * **USD**
4. Click **Select metric**.

---

# Step 4 – Configure the Alarm Threshold

## ⚙️ Define Alarm Conditions

Configure the following settings:

| Setting                      | Value              |
| ---------------------------- | ------------------ |
| Threshold Type               | Static             |
| Whenever EstimatedCharges is | Greater than       |
| Threshold Value              | **5 USD** (≈ ₹400) |

After configuring the threshold:

* Click **Next**.

---

# Step 5 – Configure Notifications

## 📧 Create an Amazon SNS Topic

Configure email notifications for the billing alarm.

1. Under **Notification**, select **In alarm**.
2. Create a new Amazon SNS topic:

   * **Topic Name:** `billing-alerts`
3. Enter your email address:

   * `your@email.com`
4. Click **Create topic**.
5. Click **Next**.

---

# Step 6 – Name and Create the Alarm

## ✅ Complete Alarm Configuration

Configure the alarm with the following details:

| Setting    | Value                    |
| ---------- | ------------------------ |
| Alarm Name | `AWS-Billing-Alert-5USD` |

Then:

1. Click **Next**.
2. Click **Create Alarm**.
3. Check your email inbox.
4. Confirm the Amazon SNS subscription by clicking the confirmation link.

> 💡 **Tip**
>
> Billing notifications will not be delivered until the Amazon SNS email subscription has been confirmed.

---

# Step 7 – Review AWS Service Limits

## 📈 Check Service Quotas

Review your AWS service limits using AWS Trusted Advisor.

1. Search for **AWS Trusted Advisor**.
2. Open **AWS Trusted Advisor** (available in the Free Basic Support plan).
3. Select **Service Limits**.
4. Review the service limits for:

   * EC2
   * RDS
   * AWS Region: **ap-south-1**

---

## 📝 Note

> **Always set a billing alarm before starting any lab.** This helps protect your AWS account from unexpected charges by notifying you when your estimated billing exceeds the configured threshold.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Enabled AWS billing alerts.
* Configured an Amazon CloudWatch billing alarm.
* Created an Amazon SNS notification topic.
* Verified the email subscription.
* Reviewed AWS service limits using AWS Trusted Advisor.

> 🎯 **Outcome**
>
> Your AWS account is now configured to notify you when estimated charges exceed **5 USD**, providing an additional layer of cost monitoring before proceeding with future AWS labs.
