# 1.3 AWS Storage Services

# Lab 7: Create an Amazon S3 Bucket, Upload Files, and Host a Static Website

> **⏱ Duration:** 30 Minutes
> **📈 Difficulty:** Beginner

---

## 📖 Overview

This lab demonstrates how to create an Amazon S3 bucket, upload website files, enable static website hosting, configure public access using a bucket policy, and verify the hosted website.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create an Amazon S3 bucket.
* Upload website files to the bucket.
* Enable Amazon S3 Static Website Hosting.
* Configure a bucket policy for public read access.
* Test a hosted static website using the S3 website endpoint.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* Access to the AWS Management Console.
* Basic HTML files:

  * `index.html`
  * `error.html`

> ⚠️ **Important**
>
> Amazon S3 bucket names must be **globally unique** across all AWS accounts.

---

# Step 1 – Create an Amazon S3 Bucket

## 🪣 Configure the Bucket

Navigate to:

**Amazon S3 → Create bucket**

Configure the following settings:

| Setting             | Value                                                   |
| ------------------- | ------------------------------------------------------- |
| Bucket Name         | `vinod-devops-website-2024` *(must be globally unique)* |
| AWS Region          | `ap-south-1`                                            |
| Object Ownership    | ACLs disabled                                           |
| Block Public Access | **Uncheck** **Block all public access**                 |
| Acknowledgement     | Check the confirmation box                              |

After completing the configuration:

* Click **Create bucket**.

> ⚠️ **Warning**
>
> Disabling **Block Public Access** allows objects to be publicly accessible. This setting should only be used for intentionally public static websites.

---

# Step 2 – Upload Website Files

## 📤 Upload HTML Files

Open the newly created bucket.

Navigate to:

**Bucket → Upload**

Upload the following files:

* `index.html`
* `error.html`

### Sample `index.html`

```html
<!DOCTYPE html>
<html>
<body style="font-family:Arial; text-align:center; padding:50px;">
    <h1>Hello from AWS S3! 🚀</h1>
    <p>Hosted by Vinod | DevOps Lab</p>
</body>
</html>
```

After uploading:

* Click **Upload**.
* Click **Close**.

---

# Step 3 – Enable Static Website Hosting

## 🌐 Configure Website Hosting

Navigate to:

**Bucket → Properties**

Scroll to:

**Static website hosting**

Configure the following:

| Setting        | Value                 |
| -------------- | --------------------- |
| Hosting        | Enable                |
| Hosting Type   | Host a static website |
| Index Document | `index.html`          |
| Error Document | `error.html`          |

After completing the configuration:

* Click **Save changes**.

> ℹ️ **Note**
>
> Record the **Bucket website endpoint URL**. You will use this URL to access the hosted website.

---

# Step 4 – Configure the Bucket Policy

## 🔓 Allow Public Read Access

Navigate to:

**Bucket → Permissions → Bucket Policy → Edit**

Paste the following bucket policy.

---

# Step 5 – Bucket Policy JSON

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::vinod-devops-website-2024/*"
    }
  ]
}
```

After pasting the policy:

* Click **Save changes**.

> 🔒 **Security Note**
>
> This policy grants **public read access** to all objects within the bucket. Use public bucket policies only for content intended to be publicly accessible, such as static websites.

---

# Step 6 – Test the Website

## ✅ Verify Static Website Hosting

Open the **Bucket Website Endpoint URL** in a web browser.

Example:

```text
http://vinod-devops-website-2024.s3-website.ap-south-1.amazonaws.com
```

Expected result:

* Your hosted HTML page is displayed successfully.

> 🎉 **Success**
>
> You should see your HTML page hosted directly from Amazon S3.

---

## 💡 Best Practices

* Use globally unique bucket names.
* Keep **Block Public Access** enabled for buckets that are **not** intended for public access.
* Enable versioning and encryption for production buckets.
* Consider using Amazon CloudFront to improve performance and add HTTPS support.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created an Amazon S3 bucket.
* Uploaded website files.
* Enabled Amazon S3 Static Website Hosting.
* Configured a bucket policy for public access.
* Accessed the website using the S3 website endpoint.

> 🎯 **Outcome**
>
> You have successfully deployed a static website using Amazon S3. This lab introduces object storage, public access configuration, and static web hosting—fundamental concepts for hosting lightweight web applications on AWS.
