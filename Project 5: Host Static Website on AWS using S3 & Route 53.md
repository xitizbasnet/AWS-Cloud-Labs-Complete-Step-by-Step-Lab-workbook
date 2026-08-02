# SECTION 2 — Real-Time Projects

# Project 5: Host Static Website on AWS using S3 & Route 53

> **Project Level:** 🟡 Intermediate
> **Architecture Type:** Serverless Static Website Hosting
> **AWS Services:** Amazon S3 + Amazon Route 53 + Amazon CloudFront + AWS Certificate Manager (ACM)

---

# Overview

In this real-time project, you will host a static website on AWS using **Amazon S3** and configure a custom domain using **Amazon Route 53**.

The project covers:

* Domain configuration using Route 53
* DNS management with external domain providers such as GoDaddy
* Static website hosting using Amazon S3
* Custom domain mapping
* HTTPS enablement using CloudFront and ACM

This architecture provides a highly available, scalable, and cost-effective solution for hosting static websites.

---

# Architecture Overview

```text id="8v3kph"
                 User Browser

                      │

                      ▼

              Custom Domain Name
              yourdomain.com

                      │

                      ▼

              Amazon Route 53
              DNS Resolution

                      │

                      ▼

             Amazon CloudFront
             HTTPS Distribution

                      │

                      ▼

              Amazon S3 Bucket
          Static Website Hosting

                      │

                      ▼

          index.html + Assets
```

---

# Learning Objectives

After completing this project, you will be able to:

* ✅ Configure DNS using Amazon Route 53
* ✅ Connect external domains with Route 53 nameservers
* ✅ Host static websites using Amazon S3
* ✅ Configure Route 53 alias records
* ✅ Enable HTTPS using CloudFront and ACM
* ✅ Serve websites securely through a custom domain

---

# Prerequisites

Before starting this project, ensure you have:

* AWS Account
* Registered domain name:

  * GoDaddy
  * Other domain registrar
* Website files:

  * `index.html`
  * CSS files
  * JavaScript files
  * Images/assets

---

# Website Hosting Architecture

```text id="4h7n3c"
Domain Registrar
(GoDaddy)

       │

       ▼

Route 53 Hosted Zone

       │

       ▼

DNS A Record

       │

       ▼

CloudFront Distribution

       │

       ▼

S3 Static Website Bucket

       │

       ▼

Website Content
```

---

# Step 1 — Buy or Use Domain

Use an existing domain or purchase a new domain.

Example:

```text id="5j9v4x"
yourdomain.com
```

---

## Using GoDaddy Domain

If you already have a GoDaddy domain:

Navigate to:

```text id="p4q8nm"
GoDaddy → My Products → Manage DNS
```

You will update the domain nameservers to Route 53 nameservers.

---

## Alternative Testing Option

For testing purposes:

* Use a free subdomain
* Use an existing test domain

---

# Step 2 — Create Hosted Zone in Route 53

📍 **Navigation**

```text id="w8f3mq"
Route 53 → Hosted zones → Create hosted zone
```

---

## Hosted Zone Configuration

| Setting     | Value              |
| ----------- | ------------------ |
| Domain Name | `yourdomain.com`   |
| Type        | Public hosted zone |

Click:

```text id="y5n7ks"
Create hosted zone
```

---

## Record Nameservers

After creation, Route 53 provides four NS records:

Example:

```text id="h2x8vp"
ns-xxxx.awsdns-xx.org

ns-xxxx.awsdns-xx.com

ns-xxxx.awsdns-xx.net

ns-xxxx.awsdns-xx.co.uk
```

Save these values.

---

# Step 3 — Update Nameservers at GoDaddy

📍 **Navigation**

```text id="7h3vnp"
GoDaddy → My Products → DNS → Change Nameservers
```

---

Replace existing nameservers with Route 53 NS records:

```text id="m5z8cq"
Route53 Nameserver 1
Route53 Nameserver 2
Route53 Nameserver 3
Route53 Nameserver 4
```

Save changes.

---

## DNS Propagation

DNS changes may take:

```text id="z4n9qs"
24–48 hours
```

to fully propagate globally.

---

# Step 4 — Create S3 Bucket with Exact Domain Name

📍 **Navigation**

```text id="8v1kfz"
S3 → Create bucket
```

---

## Bucket Configuration

Important:

The bucket name **must exactly match the domain name**.

Example:

```text id="j3m8qv"
Domain:
yourdomain.com

S3 Bucket:
yourdomain.com
```

---

## Enable Static Website Hosting

Configure:

```text id="c9x5nv"
S3 → Properties → Static website hosting
```

Enable:

```text id="k7m2pw"
Static website hosting
```

---

Configure:

| Setting        | Value        |
| -------------- | ------------ |
| Index document | `index.html` |
| Error document | `error.html` |

---

# Upload Website Files

Upload:

```text id="p7w4ks"
index.html

style.css

script.js

images/
```

---

# Configure Bucket Permissions

Set public bucket policy.

Example:

```json id="n6x2mt"
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::yourdomain.com/*"
    }
  ]
}
```

---

# Step 5 — Create Route 53 A Record

📍 **Navigation**

```text id="q9v3mh"
Route 53 → Hosted zone → Create record
```

---

## Record Configuration

| Setting     | Value       |
| ----------- | ----------- |
| Record Name | Leave empty |
| Record Type | A           |
| Alias       | Yes         |

---

## Route Traffic To

Select:

```text id="c6m1pz"
S3 website endpoint
```

Region:

```text id="r4n8vx"
ap-south-1
```

Select:

```text id="x8v5km"
your S3 bucket
```

Create record.

---

# Step 6 — Test Website

Open browser:

```text id="g3n7qk"
http://yourdomain.com
```

Expected result:

```text id="m9q2vw"
Your S3 static website loads successfully.
```

---

# Website Request Flow

```text id="p6w4sx"
User

 ↓

yourdomain.com

 ↓

Route 53 DNS

 ↓

S3 Website Endpoint

 ↓

index.html Response
```

---

# Step 7 — Add HTTPS with CloudFront

To enable HTTPS, configure Amazon CloudFront.

---

# Create CloudFront Distribution

📍 **Navigation**

```text id="h5v9mw"
CloudFront → Create Distribution
```

---

## Origin Configuration

Select:

```text id="x3q8kp"
Amazon S3 Bucket
```

---

# Request SSL Certificate

📍 **Navigation**

```text id="v7m2qx"
AWS Certificate Manager (ACM)
```

Important:

Certificate region:

```text id="n4x8vp"
us-east-1
```

---

Request certificate for:

```text id="q6m3sz"
yourdomain.com
www.yourdomain.com
```

---

# Attach Certificate to CloudFront

Configure:

```text id="r8v5mk"
CloudFront Distribution
        ↓
Custom SSL Certificate
        ↓
ACM Certificate
```

---

# Update Route 53 Record

Modify the A record:

Change:

```text id="m3q7vx"
S3 Website Endpoint
```

To:

```text id="p8n4ks"
CloudFront Distribution
```

---

# Final HTTPS Architecture

```text id="w2k9mx"
User

 │

 ▼

https://yourdomain.com

 │

 ▼

Route 53

 │

 ▼

CloudFront HTTPS

 │

 ▼

S3 Static Website
```

---

# Validation Checklist

Verify:

* ✅ Domain registered and accessible
* ✅ Route 53 hosted zone created
* ✅ GoDaddy nameservers updated
* ✅ S3 bucket created using domain name
* ✅ Static website hosting enabled
* ✅ Website files uploaded
* ✅ Route 53 alias record configured
* ✅ Website accessible through domain
* ✅ CloudFront distribution created
* ✅ HTTPS enabled successfully

---

# Production Best Practices

* 💡 Enable S3 bucket versioning for website backups.
* 💡 Use CloudFront instead of direct S3 website endpoints for production.
* 💡 Enable AWS WAF on CloudFront for additional protection.
* 💡 Use ACM certificates for automatic SSL renewal.
* 💡 Enable CloudFront caching for better performance.
* 💡 Restrict direct S3 access using Origin Access Control (OAC).
* 💡 Monitor access logs using Amazon CloudWatch and S3 logging.

---

# Troubleshooting

| Issue                        | Possible Cause          | Resolution                                      |
| ---------------------------- | ----------------------- | ----------------------------------------------- |
| Domain not loading           | DNS propagation delay   | Wait for nameserver updates to complete.        |
| Access Denied from S3        | Incorrect bucket policy | Verify public read permissions.                 |
| Route 53 cannot find bucket  | Bucket name mismatch    | Ensure bucket name exactly matches domain name. |
| HTTPS certificate fails      | Wrong ACM region        | Request CloudFront certificates in `us-east-1`. |
| CloudFront shows old content | Cache still active      | Create CloudFront cache invalidation.           |
