# 1.3 AWS Storage Services

# Lab 11: Attach and Mount an EBS Volume to Linux EC2

> **⏱ Duration:** 25 Minutes
> **📈 Difficulty:** Intermediate

---

## 📖 Overview

This lab demonstrates how to create an Amazon Elastic Block Store (EBS) volume, attach it to a Linux EC2 instance, format the volume, mount it to the Linux file system, and configure it to automatically mount after system reboots.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Create an Amazon EBS volume.
* Attach an EBS volume to a Linux EC2 instance.
* Format an uninitialized volume using the ext4 file system.
* Mount the volume to a Linux directory.
* Configure persistent mounting using `/etc/fstab`.
* Store and retrieve data from the mounted volume.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* A running Linux EC2 instance.
* SSH access to the EC2 instance.
* The EC2 key pair file (`linux-key.pem`) from previous labs.

> ⚠️ **Important**
>
> The EBS volume and EC2 instance must be located in the **same Availability Zone** before the volume can be attached.

---

# Step 1 – Create an EBS Volume

## 💾 Create the Storage Volume

Navigate to:

**EC2 → Elastic Block Store → Volumes → Create Volume**

Configure the following settings:

| Setting           | Value                                          |
| ----------------- | ---------------------------------------------- |
| Volume Type       | `gp3` (General Purpose SSD)                    |
| Size              | `10 GiB`                                       |
| Availability Zone | `ap-south-1a` *(MUST match your EC2 instance)* |

After completing the configuration:

* Click **Create volume**.

> 💡 **Learning Objective**
>
> Amazon EBS provides persistent block storage that can be attached to EC2 instances.

---

# Step 2 – Attach the EBS Volume to EC2

## 🔗 Connect the Volume to the Instance

Select the newly created volume.

Navigate to:

**Actions → Attach volume**

Configure:

| Setting     | Value                                |
| ----------- | ------------------------------------ |
| Instance    | Select your `linux-lab` EC2 instance |
| Device Name | `/dev/sdf` (default)                 |

Then:

* Click **Attach volume**.

---

# Step 3 – SSH into EC2 and Format the Volume

## 🔐 Connect to Linux EC2

Connect to your EC2 instance:

```bash id="f7xm0p"
ssh -i linux-key.pem ubuntu@<EC2_IP>
```

---

## 🔍 Check the New Volume

Run:

```bash id="g8b2aw"
lsblk
```

Expected output:

```text id="p9k4kq"
xvdf
```

The new volume will appear as:

```text id="2dz6yq"
xvdf (unformatted)
```

---

## 🧱 Format the Volume

Format the volume using the ext4 file system:

```bash id="e5bq4c"
sudo mkfs.ext4 /dev/xvdf
```

> ⚠️ **Warning**
>
> Formatting a volume removes existing data. Ensure the correct device name is selected before running the command.

---

# Step 4 – Mount the EBS Volume

## 📁 Create a Mount Point

Create a directory where the volume will be mounted:

```bash id="zq5h5x"
sudo mkdir /data
```

---

## 🔗 Mount the Volume

Mount the EBS volume:

```bash id="9pr7s5"
sudo mount /dev/xvdf /data
```

---

## ✅ Verify the Mount

Run:

```bash id="2n4f3t"
df -h | grep /data
```

and:

```bash id="a5p3l1"
lsblk
```

The volume should now appear mounted at:

```text id="z2h8xq"
/data
```

---

# Step 5 – Make the Mount Persistent After Reboots

## 🔄 Configure Automatic Mounting

By default, manually mounted volumes are not automatically mounted after a reboot.

---

## 🔍 Get the Volume UUID

Run:

```bash id="x2k8vz"
sudo blkid /dev/xvdf
```

Copy the UUID value.

---

## ✏️ Update `/etc/fstab`

Open the file:

```bash id="g1f9q4"
sudo nano /etc/fstab
```

Add the following line:

```text id="7l2z8w"
UUID=<your-uuid> /data ext4 defaults,nofail 0 2
```

---

## 🧪 Test the Configuration

Run:

```bash id="p3w7hm"
sudo mount -a
```

Verify:

```bash id="j4d8yn"
df -h
```

> 💡 **Tip**
>
> The `nofail` option allows the system to boot even if the EBS volume is temporarily unavailable.

---

# Step 6 – Use the EBS Volume

## 📄 Create and Verify Data

Create a test file:

```bash id="h6k9vr"
sudo touch /data/testfile.txt
```

Write data to the file:

```bash id="q8x3ds"
sudo sh -c 'echo "EBS Volume mounted!" > /data/testfile.txt'
```

Read the file contents:

```bash id="m2v7lc"
cat /data/testfile.txt
```

Expected output:

```text id="4s8yfn"
EBS Volume mounted!
```

---

## 💡 Best Practices

* Use separate EBS volumes for application data and operating system files.
* Select the correct Availability Zone before creating EBS volumes.
* Use meaningful mount points such as `/data`, `/backup`, or `/app`.
* Configure `/etc/fstab` carefully to prevent boot issues.
* Create EBS snapshots regularly for backup and recovery.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Created a 10 GiB gp3 EBS volume.
* Attached the volume to a Linux EC2 instance.
* Connected using SSH.
* Formatted the volume using ext4.
* Mounted the volume to `/data`.
* Configured persistent mounting using `/etc/fstab`.
* Stored and verified data on the EBS volume.

> 🎯 **Outcome**
>
> You have successfully attached and configured persistent block storage for a Linux EC2 instance using Amazon EBS. This skill is essential for managing databases, applications, logs, and enterprise workloads running on AWS.
