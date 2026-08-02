# 1.3 AWS Storage Services

# Lab 10: Deliver Content Faster via Amazon CloudFront

> **⏱ Duration:** 30 Minutes
> **📈 Difficulty:** Intermediate

---

## 📖 Overview

This lab demonstrates how to use **Amazon CloudFront** as a Content Delivery Network (CDN) to distribute website content faster to users around the world. You will configure CloudFront with an Amazon S3 website origin, deploy a distribution, test content delivery, and invalidate cached content.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create an Amazon CloudFront distribution.
* Configure an Amazon S3 website as the origin.
* Enable HTTPS redirection.
* Test website delivery through the CloudFront CDN.
* Invalidate cached content after website updates.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* An Amazon S3 static website configured from **Lab 7**.
* Public access enabled for the S3 website endpoint.
* Access to the AWS Management Console.

> ℹ️ **Note**
>
> Amazon CloudFront improves performance by caching content at AWS Edge Locations closer to users.

---

# Step 1 – Open the CloudFront Console

## 🌐 Create a CloudFront Distribution

Navigate to:

**AWS Console → Search → CloudFront**

Perform the following:

* Click **Create distribution**.

---

# Step 2 – Configure the Origin

## 🔗 Configure S3 Website Origin

Configure the origin settings:

| Setting                | Value                                  |
| ---------------------- | -------------------------------------- |
| Origin Domain          | Select your S3 bucket website endpoint |
| Origin Access          | Public                                 |
| Default Cache Behavior | GET, HEAD                              |
| Viewer Protocol Policy | Redirect HTTP to HTTPS                 |

> ⚠️ **Important**
>
> For this lab, select the **S3 bucket website endpoint** as the origin.
>
> **Do NOT select the S3 bucket directly.**

---

# Step 3 – Configure the CloudFront Distribution

## ⚙️ Distribution Settings

Configure the following:

| Setting             | Value            |
| ------------------- | ---------------- |
| Default Root Object | `index.html`     |
| Description         | `S3-Website-CDN` |

After completing the configuration:

* Click **Create distribution**.

---

## ⏳ Wait for Deployment

CloudFront deployment may take:

```text
10-15 minutes
```

Verify the distribution status:

```text
Status: Enabled
```

> 💡 **Tip**
>
> CloudFront distributions require time to deploy across AWS global Edge Locations.

---

# Step 4 – Test CloudFront Delivery

## ✅ Verify CDN Access

After deployment:

1. Copy the CloudFront distribution domain name.

Example:

```text
xxxx.cloudfront.net
```

2. Open the URL in a web browser.

Expected result:

* Your website loads successfully through the global CDN.

Compare:

* Direct S3 website URL performance.
* CloudFront distribution URL performance.

> 🎉 **Success**
>
> Your website is now delivered through Amazon CloudFront using AWS global content delivery infrastructure.

---

# Step 5 – Invalidate CloudFront Cache

## 🔄 Clear Cached Content After Updates

When website files are updated, create a CloudFront cache invalidation.

Navigate to:

**CloudFront → Distribution → Invalidations → Create**

Configure:

| Setting      | Value |
| ------------ | ----- |
| Object Paths | `/*`  |

Then:

* Click **Create invalidation**.

> ℹ️ **Note**
>
> Cache invalidation forces CloudFront Edge Locations to retrieve the latest version of your content from the origin.

---

## 💡 Best Practices

* Use HTTPS redirection for secure content delivery.
* Configure cache behaviors based on application requirements.
* Use cache invalidations only when required, as frequent invalidations may increase costs.
* Combine CloudFront with S3 for scalable and high-performance static websites.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created an Amazon CloudFront distribution.
* Configured an S3 website endpoint as the origin.
* Enabled HTTP-to-HTTPS redirection.
* Deployed and tested content delivery through CloudFront.
* Created a cache invalidation request.

> 🎯 **Outcome**
>
> You have successfully configured Amazon CloudFront to accelerate website delivery using AWS global CDN infrastructure, improving performance, availability, and user experience.
