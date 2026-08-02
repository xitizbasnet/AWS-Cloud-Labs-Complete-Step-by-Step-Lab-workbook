# 1.1 AWS Basic Labs

# Lab 1: Create an AWS Free Trial Account

> **⏱ Duration:** 20 Minutes
> **📈 Difficulty:** Beginner

---

## 📖 Overview

This lab guides you through creating an AWS Free Tier account, configuring basic billing awareness, and preparing your AWS environment for future labs.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create a new AWS account using Free Tier.
* Configure billing alerts from day one.
* Understand AWS Free Tier limits.
* Set the default AWS Region to **ap-south-1 (Mumbai)**.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* A valid email address (preferably dedicated for AWS).
* A mobile phone capable of receiving SMS verification (OTP).
* A valid Credit/Debit card (required for account verification).

> ℹ️ **Note**
>
> AWS requires a payment method during account creation. You will **not be charged** if you remain within the AWS Free Tier limits.

---

# Step 1 – Go to the AWS Website

## 🌐 Access the AWS Portal

Open your preferred web browser.

```text
https://aws.amazon.com
```

* Click **Create an AWS Account**.

---

# Step 2 – Fill Account Details

## 👤 Create Your AWS Account

Enter the following information:

* **Root email:** Use a dedicated email.
* **AWS account name:** `YourName-DevOps`

After entering the information:

* Click **Continue**.

---

# Step 3 – Enter Contact Information

## 📝 Provide Personal Details

Configure the following:

* **Account type:** Personal
* Fill in:

  * Full name
  * Phone
  * Address
  * Country: India

After completing the form:

* Click **Continue**.

---

# Step 4 – Add Payment Method

## 💳 Configure Billing Information

A valid payment method is required for AWS account verification.

* Credit/Debit card required (**will NOT be charged for Free Tier usage**).
* Enter card details.
* Click **Verify and Add**.

> ⚠️ **Important**
>
> AWS may place a temporary authorization hold on your card during verification. This is not a usage charge.

---

# Step 5 – Confirm Identity

## 📱 Verify Your Account

Complete identity verification by following these steps:

* Enter phone number.
* Receive OTP.
* Enter OTP.
* Select **Support Plan:** Basic (Free).
* Click **Complete sign up**.

---

# Step 6 – Set Default Region

## 🌍 Configure the AWS Region

Sign in to the AWS Management Console.

```text
https://console.aws.amazon.com
```

Perform the following:

* Login to your AWS account.
* In the top-right corner, select the AWS Region.
* Choose:

```text
ap-south-1 (Asia Pacific - Mumbai)
```

This is the closest AWS Region to users located in Mumbai.

> 💡 **Tip**
>
> Keeping all labs in the same AWS Region helps avoid confusion and unexpected resource placement.

---

# Step 7 – Explore the Free Tier Dashboard

## 📊 Review Free Tier Usage

Navigate through the AWS Console:

1. Click your **Account Name** (top-right).
2. Open **Billing Dashboard**.
3. From the left navigation pane, select **Free Tier**.
4. Review the AWS services included in the Free Tier, such as:

   * EC2
   * S3
   * RDS
   * Lambda
   * And more.

---

## 💡 Tip

The AWS Free Tier includes:

* **750 hours/month** of EC2 `t2.micro`
* **5 GB** Amazon S3 storage
* **25 GB** Amazon DynamoDB
* **1 Million** AWS Lambda requests

> ✅ **Best Practice**
>
> Always check AWS Free Tier limits before running any lab to avoid unexpected charges.
