# 1.5 AWS Networking Services

# LAB 15: Create a Custom Virtual Private Cloud (VPC)

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 45 Minutes

---

# Overview

In this lab, you will create a **custom Amazon Virtual Private Cloud (VPC)** from scratch, including public and private subnets, an Internet Gateway (IGW), a NAT Gateway, custom route tables, and an EC2 instance.

This lab demonstrates how to build a secure and scalable network architecture that separates internet-facing resources from private resources while enabling controlled outbound internet access.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create a custom Amazon VPC
* ✅ Configure public and private subnets
* ✅ Create and attach an Internet Gateway (IGW)
* ✅ Configure custom Route Tables
* ✅ Deploy a NAT Gateway
* ✅ Launch an EC2 instance inside the custom VPC
* ✅ Validate internet connectivity

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* EC2 Key Pair (`linux-key`)
* Permissions to create:

  * VPC
  * Subnets
  * Route Tables
  * Internet Gateway
  * NAT Gateway
  * Elastic IP
  * EC2 Instances

---

# Architecture Overview

```text
                         Internet
                             │
                    Internet Gateway
                             │
                     Public Route Table
                             │
        ┌────────────────────┴────────────────────┐
        │                                         │
        ▼                                         ▼
 Public Subnet (10.0.1.0/24)              NAT Gateway
        │                                         │
        │                                         │
     EC2 Instance                          Private Route Table
                                                  │
                                                  ▼
                                  Private Subnet (10.0.2.0/24)
```

---

# Step 1 – Create VPC

📍 **Navigation**

```text
VPC → Your VPCs → Create VPC
```

Configure the following settings:

| Setting   | Value         |
| --------- | ------------- |
| Name tag  | `devops-vpc`  |
| IPv4 CIDR | `10.0.0.0/16` |
| IPv6      | No IPv6       |
| Tenancy   | Default       |

After completing the configuration:

* Click **Create VPC**

---

# Step 2 – Create Subnets

📍 **Navigation**

```text
VPC → Subnets → Create subnet
```

Select the VPC:

| Setting | Value        |
| ------- | ------------ |
| VPC     | `devops-vpc` |

Create the following subnets:

### Public Subnet

| Setting           | Value              |
| ----------------- | ------------------ |
| Name              | `public-subnet-1a` |
| Availability Zone | `ap-south-1a`      |
| CIDR Block        | `10.0.1.0/24`      |

### Private Subnet

| Setting           | Value               |
| ----------------- | ------------------- |
| Name              | `private-subnet-1b` |
| Availability Zone | `ap-south-1b`       |
| CIDR Block        | `10.0.2.0/24`       |

After configuring both subnets:

* Click **Create subnets**

---

# Step 3 – Create Internet Gateway

📍 **Navigation**

```text
VPC → Internet Gateways → Create
```

Configure the following settings:

| Setting | Value        |
| ------- | ------------ |
| Name    | `devops-igw` |

After creating the Internet Gateway:

1. Select the Internet Gateway.
2. Choose **Actions**.
3. Select **Attach to VPC**.
4. Choose **`devops-vpc`**.
5. Click **Attach**.

> **Note**
>
> An Internet Gateway enables communication between resources in the VPC and the public internet.

---

# Step 4 – Create Route Tables

📍 **Navigation**

```text
VPC → Route Tables → Create
```

Create the public route table:

| Setting | Value        |
| ------- | ------------ |
| Name    | `public-rt`  |
| VPC     | `devops-vpc` |

Edit the routes:

| Destination | Target                          |
| ----------- | ------------------------------- |
| `0.0.0.0/0` | Internet Gateway → `devops-igw` |

Save the route configuration.

Associate the route table with the public subnet:

```text
Subnet Associations → Edit → Associate public-subnet-1a
```

---

# Step 5 – Create NAT Gateway (for Private Subnet)

📍 **Navigation**

```text
VPC → NAT Gateways → Create
```

Configure the following settings:

| Setting      | Value              |
| ------------ | ------------------ |
| Name         | `devops-nat`       |
| Subnet       | `public-subnet-1a` |
| Connectivity | Public             |

Allocate an Elastic IP and then:

* Click **Create**

Next, create a private route table.

| Setting | Value        |
| ------- | ------------ |
| Name    | `private-rt` |
| VPC     | `devops-vpc` |

Add the following route:

| Destination | Target                     |
| ----------- | -------------------------- |
| `0.0.0.0/0` | NAT Gateway (`devops-nat`) |

Associate the private route table with:

* `private-subnet-1b`

> **Important**
>
> The NAT Gateway must reside in a **public subnet** to provide outbound internet access for resources located in private subnets.

---

# Step 6 – Launch EC2 in Custom VPC

📍 **Navigation**

```text
EC2 → Launch Instance
```

Under **Network settings**, select **Edit** and configure:

| Setting               | Value                                   |
| --------------------- | --------------------------------------- |
| VPC                   | `devops-vpc`                            |
| Subnet                | `public-subnet-1a`                      |
| Auto-assign public IP | Enable                                  |
| Security Group        | Create new in `devops-vpc` (SSH + HTTP) |

After completing the configuration:

* Click **Launch**

---

# Step 7 – Test Connectivity

Connect to the EC2 instance using SSH.

```bash
ssh -i linux-key.pem ubuntu@<EC2_PUBLIC_IP>
```

Verify internet connectivity:

```bash
ping 8.8.8.8
```

Expected result:

```text
Should work (Internet via IGW)
```

Test HTTP connectivity:

```bash
curl http://google.com
```

Expected result:

```text
Should respond
```

> **Success**
>
> Successful responses confirm that the EC2 instance has internet access through the Internet Gateway (IGW).

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Custom VPC created successfully
* ✅ Public subnet created
* ✅ Private subnet created
* ✅ Internet Gateway attached to the VPC
* ✅ Public Route Table configured
* ✅ NAT Gateway deployed
* ✅ Private Route Table configured
* ✅ EC2 instance launched in the custom VPC
* ✅ SSH connection successful
* ✅ Internet connectivity verified using `ping` and `curl`

---

# Best Practices

* 💡 Use separate public and private subnets for improved security.
* 💡 Place internet-facing resources in public subnets and backend resources in private subnets.
* 💡 Deploy NAT Gateways in public subnets to enable secure outbound internet access for private resources.
* 💡 Use descriptive names for VPC components to simplify management.
* 💡 Regularly review route tables and security groups to ensure proper network segmentation.

---

# Troubleshooting

| Issue                                     | Possible Cause                                   | Resolution                                                                                                                         |
| ----------------------------------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| Unable to connect via SSH                 | Security Group or key pair issue                 | Verify that SSH (Port 22) is allowed and the correct key pair is used.                                                             |
| No internet access from the EC2 instance  | Internet Gateway or route table misconfiguration | Confirm the Internet Gateway is attached and the public route table contains a `0.0.0.0/0` route to the IGW.                       |
| Private subnet cannot access the internet | NAT Gateway or private route table issue         | Verify the NAT Gateway is deployed in the public subnet and the private route table routes `0.0.0.0/0` traffic to the NAT Gateway. |
| NAT Gateway creation fails                | Elastic IP not allocated                         | Allocate an Elastic IP before creating the NAT Gateway.                                                                            |
| EC2 instance does not receive a public IP | Auto-assign public IP disabled                   | Enable **Auto-assign public IP** when launching the EC2 instance in the public subnet.                                             |
