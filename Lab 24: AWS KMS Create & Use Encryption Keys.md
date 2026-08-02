# 1.10 AWS Security Services

# Lab 24: AWS KMS — Create & Use Encryption Keys

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will configure **AWS Key Management Service (AWS KMS)** to create and manage encryption keys, encrypt Amazon S3 objects, encrypt Amazon EBS volumes, and perform encryption and decryption operations using the AWS CLI.

AWS KMS provides centralized control over cryptographic keys used to protect data across AWS services.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create a customer managed KMS key
* ✅ Configure KMS permissions
* ✅ Enable S3 server-side encryption using KMS
* ✅ Encrypt EBS volumes using KMS keys
* ✅ Perform encryption and decryption using AWS CLI
* ✅ Understand AWS data encryption workflows

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* IAM user with permissions to access:

  * AWS KMS
  * Amazon S3
  * Amazon EC2
  * AWS CLI
* AWS CLI installed and configured
* Existing S3 bucket (for encryption testing)
* Running EC2 instance (for EBS encryption testing)

---

# Architecture Overview

```text id="w5s7km"
                  AWS KMS
                    │
          Customer Managed Key
          alias: devops-key-2024
                    │
      ┌─────────────┼─────────────┐
      │             │             │
      ▼             ▼             ▼

     S3            EBS        AWS CLI
  Objects       Volumes    Encrypt/Decrypt

      │             │
      └────── Encryption ──────┘
```

---

# Step 1 – Create KMS Key

📍 **Navigation**

```text id="c7m1xq"
KMS → Customer managed keys → Create key
```

Configure the following settings:

| Setting              | Value               |
| -------------------- | ------------------- |
| Key Type             | Symmetric           |
| Encryption Algorithm | AES-256             |
| Key Usage            | Encrypt and decrypt |
| Alias                | `devops-key-2024`   |
| Key Administrator    | Your IAM user       |
| Key User             | Your IAM user       |

After completing the configuration:

* Click **Finish**
* Click **Create key**

> **Note**
>
> A customer managed KMS key provides full control over key policies, permissions, rotation settings, and lifecycle management.

---

# Step 2 – Encrypt S3 Objects with KMS

Enable KMS encryption for an S3 bucket.

📍 **Navigation**

```text id="9t7j2v"
S3 → Bucket → Properties → Default encryption
```

Configure:

| Setting                | Value             |
| ---------------------- | ----------------- |
| Server-side encryption | SSE-KMS           |
| AWS KMS Key            | `devops-key-2024` |

After configuration:

* Click **Save changes**

Expected result:

* All newly uploaded objects are automatically encrypted using the selected KMS key.

> **Success**
>
> Amazon S3 now uses AWS KMS to encrypt uploaded objects at rest.

---

# Step 3 – Encrypt EBS Volume with KMS

Create an encrypted EBS volume.

📍 **Navigation**

```text id="7n4k9p"
EC2 → Volumes → Create volume
```

Configure:

| Setting    | Value             |
| ---------- | ----------------- |
| Encryption | Enabled           |
| KMS Key    | `devops-key-2024` |

After configuration:

1. Click **Create volume**.
2. Attach the volume to an EC2 instance.

Expected result:

* All data stored on the EBS volume is encrypted using AWS KMS.

> **Important**
>
> EBS encryption protects data at rest, snapshots, and data transferred between EC2 and encrypted EBS volumes.

---

# Step 4 – CLI Encryption/Decryption

AWS KMS can also be used directly through the AWS CLI.

---

## Encrypt a String

Run the following command:

```bash id="zq2y5h"
aws kms encrypt \
--key-id alias/devops-key-2024 \
--plaintext 'MySecretPassword' \
--output text --query CiphertextBlob \
--region ap-south-1 > encrypted.b64
```

Expected result:

* The plaintext value is encrypted.
* The encrypted output is stored in:

```text id="z7y1wp"
encrypted.b64
```

---

## Decrypt the Encrypted Data

Run the following command:

```bash id="7m0x4g"
aws kms decrypt \
--ciphertext-blob fileb://encrypted.b64 \
--output text --query Plaintext \
--region ap-south-1 | base64 -d
```

Expected output:

```text id="4d8j2q"
MySecretPassword
```

> **Success**
>
> The encrypted data has been successfully decrypted using the AWS KMS key.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Customer managed KMS key created
* ✅ KMS alias configured as `devops-key-2024`
* ✅ S3 default encryption enabled with SSE-KMS
* ✅ New S3 uploads encrypted using KMS
* ✅ EBS volume encrypted using KMS
* ✅ AWS CLI encryption command completed successfully
* ✅ AWS CLI decryption returned the original value

---

# Best Practices

* 💡 Use customer managed KMS keys when you need complete control over key policies and lifecycle management.
* 💡 Enable automatic key rotation for long-term key security.
* 💡 Follow the principle of least privilege when assigning KMS key permissions.
* 💡 Avoid sharing KMS keys directly; control access using IAM policies and key policies.
* 💡 Use separate KMS keys for different environments such as development, testing, and production.
* 💡 Monitor KMS usage through AWS CloudTrail logs.

---

# Troubleshooting

| Issue                             | Possible Cause                                   | Resolution                                                                          |
| --------------------------------- | ------------------------------------------------ | ----------------------------------------------------------------------------------- |
| Unable to create KMS key          | Missing KMS permissions                          | Verify that your IAM user has permissions to create and manage KMS keys.            |
| S3 encryption update fails        | Insufficient KMS permissions                     | Confirm that the IAM user has access to use the selected KMS key.                   |
| EBS volume encryption unavailable | Incorrect region or key selection                | Verify that the KMS key exists in the same AWS Region as the EBS volume.            |
| CLI encryption command fails      | AWS CLI not configured correctly                 | Verify AWS credentials, region settings, and KMS permissions.                       |
| Decryption fails                  | Incorrect ciphertext file or missing permissions | Confirm the encrypted file is valid and the IAM user can decrypt using the KMS key. |
