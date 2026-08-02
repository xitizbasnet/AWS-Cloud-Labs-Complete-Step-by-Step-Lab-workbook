# 1.1 AWS Basic Labs

# Lab 3: Create and Connect to a Windows EC2 Machine

> **⏱ Duration:** 30 Minutes
> **📈 Difficulty:** Beginner

---

## 📖 Overview

This lab walks you through launching a Microsoft Windows Server instance on Amazon EC2, securely connecting using Remote Desktop Protocol (RDP), and performing basic verification inside the Windows Server environment.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Launch a Windows EC2 instance.
* Create and download an EC2 key pair.
* Retrieve the Windows Administrator password.
* Connect to the instance using Remote Desktop Protocol (RDP).
* Verify the Windows Server installation.
* Safely stop the EC2 instance to avoid unnecessary charges.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS Free Tier account.
* Access to the AWS Management Console.
* A Windows computer with Remote Desktop Connection (or another compatible RDP client).
* Permission to download files to your local machine.

> ⚠️ **Important**
>
> Keep your EC2 key pair (`.pem`) file in a secure location. It is required to decrypt the Windows Administrator password.

---

# Step 1 – Launch a Windows EC2 Instance

## 🖥️ Create the EC2 Instance

Navigate to the Amazon EC2 console and configure the instance using the following settings.

| Setting        | Value                                                   |
| -------------- | ------------------------------------------------------- |
| Name           | `windows-lab`                                           |
| AMI            | Microsoft Windows Server 2022 Base (Free tier eligible) |
| Instance Type  | `t2.micro`                                              |
| Key Pair       | Create new → `windows-key` → RSA → `.pem` → **Create**  |
| Security Group | Allow **RDP (3389)** → **My IP**                        |

After completing the configuration:

* Click **Launch Instance**.

---

# Step 2 – Wait for the Instance to Become Ready

## ⏳ Verify Instance Status

Navigate to:

**EC2 → Instances**

Wait until the instance status displays:

```text
2/2 Status Checks Passed
```

> ℹ️ **Note**
>
> This process typically takes approximately **3 minutes**.

Record the following information:

* **Public IPv4 Address**

You will use this address to connect to the instance.

---

# Step 3 – Retrieve the Windows Administrator Password

## 🔐 Decrypt the Password

Perform the following steps:

1. Select the EC2 instance.
2. Choose:

   * **Actions**
   * **Security**
   * **Get Windows Password**
3. Upload your **windows-key.pem** file.
4. Click **Decrypt Password**.
5. Copy the generated Administrator password.

> 💡 **Tip**
>
> Store the decrypted password securely until you successfully connect to the Windows instance.

---

# Step 4 – Connect Using Remote Desktop (RDP)

## 🌐 Access the Windows Server

From the EC2 console:

1. Select the instance.
2. Click **Connect**.
3. Open the **RDP Client** tab.
4. Click **Download remote desktop file**.
5. Open the downloaded `.rdp` file.

Use the following credentials:

| Setting  | Value                        |
| -------- | ---------------------------- |
| Username | `Administrator`              |
| Password | Paste the decrypted password |

Then:

* Click **Connect**.
* Accept the security certificate when prompted.

> ✅ **Success**
>
> You are now connected to your Amazon EC2 Windows Server instance.

---

# Step 5 – Explore Windows Server

## 🖥️ Verify the Operating System

Once connected:

1. Open **Server Manager**.
2. Open **Windows PowerShell** as **Administrator**.
3. Run the following command:

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, OsArchitecture
```

This command displays:

* Windows Product Name
* Operating System Architecture

> 💡 **Learning Objective**
>
> Familiarize yourself with the Windows Server environment before installing applications or configuring services.

---

# Step 6 – Stop the EC2 Instance

## 🛑 Preserve the Instance

Return to the AWS Management Console.

Navigate to:

**EC2 → Instances**

Perform the following steps:

1. Select the EC2 instance.
2. Choose:

   * **Instance State**
   * **Stop**

> ⚠️ **Important**
>
> **Do NOT terminate the instance.** Stopping the instance preserves your work, allowing you to start it again later while helping avoid unnecessary compute charges.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Launched a Microsoft Windows Server 2022 EC2 instance.
* Created and downloaded an EC2 key pair.
* Retrieved and decrypted the Windows Administrator password.
* Connected to the instance using Remote Desktop Protocol (RDP).
* Verified the Windows Server installation using PowerShell.
* Stopped the EC2 instance while preserving the environment for future use.

> 🎯 **Outcome**
>
> You have successfully deployed, accessed, and managed a Windows Server instance on Amazon EC2, providing a foundation for future Windows-based AWS labs.
