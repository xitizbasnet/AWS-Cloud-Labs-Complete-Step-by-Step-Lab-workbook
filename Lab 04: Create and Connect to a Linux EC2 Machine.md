# 1.1 AWS Basic Labs

# Lab 4: Create and Connect to a Linux EC2 Machine

> **⏱ Duration:** 25 Minutes
> **📈 Difficulty:** Beginner

---

## 📖 Overview

This lab demonstrates how to launch an Ubuntu Server instance on Amazon EC2, securely connect using SSH, perform basic Linux system verification, and update the operating system.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Launch an Ubuntu EC2 instance.
* Create and secure an EC2 key pair.
* Connect to the instance using SSH.
* Verify basic Linux system information.
* Update the operating system using the package manager.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS Free Tier account.
* Access to the AWS Management Console.
* An SSH client:

  * **Linux/macOS:** Terminal
  * **Windows:** PowerShell or OpenSSH
* Permission to download files to your local machine.

> ⚠️ **Important**
>
> Store the downloaded **`linux-key.pem`** file securely. It is required each time you connect to the EC2 instance using SSH.

---

# Step 1 – Launch a Linux EC2 Instance

## 🖥️ Create the EC2 Instance

Navigate to the Amazon EC2 console and configure the instance using the following settings.

| Setting        | Value                                            |
| -------------- | ------------------------------------------------ |
| Name           | `linux-lab`                                      |
| AMI            | Ubuntu Server 22.04 LTS (Free tier eligible)     |
| Instance Type  | `t2.micro`                                       |
| Key Pair       | Create → `linux-key` → RSA → `.pem` → **Create** |
| Security Group | Allow **SSH (22)** → **My IP**                   |

After completing the configuration:

* Click **Launch Instance**.

---

# Step 2 – Set Key File Permissions

## 🔐 Secure the Private Key

Before connecting, restrict access to the private key file.

### Linux/macOS

Run the following command:

```bash
chmod 400 linux-key.pem
```

### Windows PowerShell

Run the following command:

```powershell
icacls linux-key.pem /inheritance:r /grant:r "$env:USERNAME:R"
```

> 💡 **Tip**
>
> Proper permissions protect the private key and are required by SSH clients before establishing a connection.

---

# Step 3 – Connect to the EC2 Instance Using SSH

## 🌐 Establish a Secure Shell (SSH) Session

Retrieve the **Public IPv4 Address** from the EC2 console.

Run the following command:

```bash
ssh -i linux-key.pem ubuntu@<PUBLIC_IP>
```

When prompted:

* Type **`yes`** to accept the SSH fingerprint.

After a successful connection, you should see a prompt similar to:

```text
ubuntu@ip-xxx-xxx-xxx-xxx
```

> ✅ **Success**
>
> You are now connected to your Ubuntu EC2 instance.

---

# Step 4 – Perform Basic System Verification

## 🖥️ Verify the Linux Environment

Run the following commands to verify the instance configuration.

### Display the Current User

```bash
whoami
```

Expected output:

```text
ubuntu
```

---

### Display the Hostname

```bash
hostname
```

Displays the instance hostname.

---

### Display Linux Kernel Information

```bash
uname -a
```

Displays Linux kernel and operating system information.

---

### Display Disk Usage

```bash
df -h
```

Displays available and used disk space.

---

### Display Memory Usage

```bash
free -m
```

Displays system memory usage in megabytes.

---

### Display the Public IP Address

```bash
curl http://checkip.amazonaws.com
```

Displays the public IP address assigned to the EC2 instance.

---

# Step 5 – Install System Updates

## 📦 Update the Operating System

Run the following command to update package information and install available updates.

```bash
sudo apt update && sudo apt upgrade -y
```

> ℹ️ **Note**
>
> This process typically takes **2–3 minutes**, depending on network speed and the number of available updates.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Launched an Ubuntu Server 22.04 LTS EC2 instance.
* Created and secured an EC2 SSH key pair.
* Connected securely to the instance using SSH.
* Verified system information using common Linux commands.
* Updated the operating system with the latest available packages.

> 🎯 **Outcome**
>
> You have successfully deployed, accessed, and administered a Linux EC2 instance on AWS. This provides the foundation for future labs involving web servers, automation, scripting, and infrastructure management.
