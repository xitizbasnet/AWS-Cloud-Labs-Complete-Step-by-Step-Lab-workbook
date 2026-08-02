# 1.10 AWS Security Services

# Lab 25: Block Web Traffic with AWS WAF

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will configure **AWS Web Application Firewall (AWS WAF)** to protect a web application from common web-based attacks.

You will create a **Web ACL (Access Control List)**, add AWS Managed Rule Groups, configure default traffic actions, create a custom IP blocking rule, and test WAF protection.

AWS WAF helps protect applications against threats such as:

* SQL Injection attacks
* Malicious IP addresses
* Common OWASP Top 10 vulnerabilities
* Suspicious web requests

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an AWS WAF Web ACL
* ✅ Associate WAF with AWS resources
* ✅ Configure AWS Managed Rule Groups
* ✅ Create custom IP blocking rules
* ✅ Monitor blocked requests
* ✅ Protect applications from common web attacks

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Web application hosted on:

  * Application Load Balancer, or
  * Amazon API Gateway
* IAM permissions for:

  * AWS WAF
  * Elastic Load Balancing
  * API Gateway
* Access to AWS Management Console

---

# Architecture Overview

```text id="p7c9mz"
                Internet Users
                      │
                      ▼
                 AWS WAF Web ACL
                      │
        ┌─────────────┴─────────────┐
        │                           │
        ▼                           ▼

 Managed Rules                Custom Rules
        │                           │
        │                           │
 OWASP Protection          Block Specific IPs
 SQL Injection             Suspicious Traffic

                      │
                      ▼
              Application Load Balancer
                      │
                      ▼
              Web Application
```

---

# Step 1 – Create WAF Web ACL

📍 **Navigation**

```text id="3h7q2m"
WAF & Shield → Web ACLs → Create web ACL
```

Configure the Web ACL:

| Setting       | Value              |
| ------------- | ------------------ |
| Web ACL Name  | `devops-waf`       |
| Resource Type | Regional resources |
| Region        | `ap-south-1`       |

Add AWS resources:

* Select your:

  * Load Balancer, or
  * API Gateway

After selecting the resource:

* Continue to the next step.

> **Note**
>
> A Web ACL defines the collection of rules that AWS WAF uses to inspect and control incoming web requests.

---

# Step 2 – Add Managed Rule Groups

Add AWS Managed Rule Groups.

📍 **Navigation**

```text id="6m4x1s"
Add rules → Add managed rule groups
```

Select the following AWS Managed Rules:

| Rule Group                              | Purpose                                |
| --------------------------------------- | -------------------------------------- |
| `AWSManagedRulesCommonRuleSet`          | OWASP Top 10 protection                |
| `AWSManagedRulesAmazonIpReputationList` | Blocks known malicious IP addresses    |
| `AWSManagedRulesSQLiRuleSet`            | Protects against SQL Injection attacks |

After selecting the rules:

* Click **Add rules**
* Click **Next**

> **Note**
>
> AWS Managed Rule Groups provide pre-configured security rules maintained by AWS security experts.

---

# Step 3 – Set Default Action

Configure the default traffic behavior.

| Setting        | Value |
| -------------- | ----- |
| Default Action | Allow |

Meaning:

```text id="f8y2kt"
Allow normal traffic
Block only requests matching WAF rules
```

After configuration:

* Click **Create web ACL**

> **Success**
>
> Your application is now protected by AWS WAF managed security rules.

---

# Step 4 – Add Custom IP Block Rule

Create a custom rule to block specific IP addresses.

📍 **Navigation**

```text id="v2d8rx"
Web ACL → Rules → Add rule → Rule builder
```

Configure the rule:

| Setting        | Value                 |
| -------------- | --------------------- |
| Rule Name      | `block-suspicious-ip` |
| Rule Type      | Regular rule          |
| Statement Type | IP set                |
| Action         | Block                 |

---

## Create IP Set

Create:

```text id="6w1j9q"
IP Set Name: blocked-ips
```

Add the IP address:

```text id="z3p5kc"
1.2.3.4/32
```

Save the rule.

> **Note**
>
> An IP set allows AWS WAF to maintain a list of addresses that should be allowed or blocked.

---

# Step 5 – Test WAF

Perform the following tests:

## Test Allowed Traffic

Access your application from an allowed IP address.

Expected result:

```text id="m2d8kp"
Website loads successfully
```

---

## Check Blocked Requests

📍 **Navigation**

```text id="7n3v8k"
WAF Dashboard → Sampled requests
```

Review:

* Blocked requests
* Rule matches
* Request details

Expected result:

```text id="h5m8qw"
Blocked requests appear in AWS WAF monitoring
```

> **Success**
>
> AWS WAF is now protecting your application against common web attacks and unwanted traffic.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ AWS WAF Web ACL created
* ✅ Web ACL associated with Load Balancer or API Gateway
* ✅ AWS Managed Rule Groups enabled
* ✅ Default action configured as Allow
* ✅ Custom IP blocking rule created
* ✅ Blocked IP added to IP Set
* ✅ WAF dashboard shows request activity

---

# Best Practices

* 💡 Start with AWS Managed Rule Groups before creating custom rules.
* 💡 Use AWS WAF logging with Amazon CloudWatch or Amazon S3 for security analysis.
* 💡 Enable rate-based rules to protect against excessive requests.
* 💡 Regularly review blocked requests before adjusting rules.
* 💡 Use IP reputation lists and managed protections for internet-facing applications.
* 💡 Test WAF rules in **Count mode** before enabling blocking in production.

---

# Troubleshooting

| Issue                           | Possible Cause                       | Resolution                                                                  |
| ------------------------------- | ------------------------------------ | --------------------------------------------------------------------------- |
| WAF does not block traffic      | Web ACL not associated with resource | Verify the Web ACL is attached to the correct Load Balancer or API Gateway. |
| Legitimate users are blocked    | Rule too restrictive                 | Review sampled requests and adjust rule conditions.                         |
| Managed rules cannot be added   | Missing permissions                  | Verify IAM permissions for AWS WAF management.                              |
| IP block rule does not work     | Incorrect IP format                  | Confirm the IP uses CIDR notation, such as `1.2.3.4/32`.                    |
| No requests appear in dashboard | No traffic reaching the resource     | Generate test traffic and verify resource association.                      |
