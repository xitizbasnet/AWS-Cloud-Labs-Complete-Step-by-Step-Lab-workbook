# 1.5 AWS Networking Services

# LAB 16: VPC Peering Connection

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will configure a **VPC Peering Connection** between two Amazon VPCs within the same AWS account. After establishing the peering connection, you will update route tables and security groups to enable private communication between EC2 instances in both VPCs.

VPC Peering allows resources in separate VPCs to communicate privately using their private IP addresses without traversing the public internet.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create a second Amazon VPC
* ✅ Launch an EC2 instance in the second VPC
* ✅ Create a VPC Peering Connection
* ✅ Accept a peering request
* ✅ Configure route tables for inter-VPC communication
* ✅ Update security groups for private connectivity
* ✅ Validate communication using private IP addresses

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Existing VPC:

  * `devops-vpc`
  * CIDR: `10.0.0.0/16`
* EC2 instance running in `devops-vpc`
* Permissions to create:

  * VPCs
  * Subnets
  * EC2 Instances
  * VPC Peering Connections
  * Route Tables
  * Security Groups

---

# Architecture Overview

```text
                   VPC Peering Connection
                 (vpc-a-to-vpc-b)
        ┌───────────────────────────────────┐
        │                                   │
        ▼                                   ▼

   VPC-A (devops-vpc)                 VPC-B (vpc-b)
   10.0.0.0/16                        10.1.0.0/16
        │                                   │
        ▼                                   ▼
    EC2 Instance                     EC2 Instance
    Private IP                       Private IP

     Communication over Private Network
```

---

# Step 1 – Create Second VPC

Create a new VPC to establish the peering connection.

Configure the following resources:

### Create VPC-B

| Setting    | Value         |
| ---------- | ------------- |
| Name       | `vpc-b`       |
| CIDR Block | `10.1.0.0/16` |

### Create Subnet

| Setting     | Value         |
| ----------- | ------------- |
| Subnet CIDR | `10.1.1.0/24` |
| VPC         | `vpc-b`       |

### Launch EC2 Instance

Launch an EC2 instance inside **vpc-b**.

> **Note**
>
> Only a **private IP address** is required for this lab.

---

# Step 2 – Create Peering Connection

📍 **Navigation**

```text
VPC → Peering Connections → Create
```

Configure the following settings:

| Setting       | Value                      |
| ------------- | -------------------------- |
| Name          | `vpc-a-to-vpc-b`           |
| Requester VPC | `devops-vpc (10.0.0.0/16)` |
| Accepter VPC  | `vpc-b (10.1.0.0/16)`      |
| Account       | Same AWS Account           |

After completing the configuration:

* Click **Create peering connection**

---

# Step 3 – Accept Peering Request

📍 **Navigation**

```text
Peering Connections → Select Connection → Actions → Accept Request
```

Perform the following steps:

1. Select the pending peering connection.
2. Click **Actions**.
3. Select **Accept Request**.
4. Confirm the request.

> **Important**
>
> The peering connection remains inactive until it is accepted.

---

# Step 4 – Update Route Tables

To enable traffic between both VPCs, update the route tables.

### Update Route Table for VPC-A

Add the following route:

| Destination   | Target                                |
| ------------- | ------------------------------------- |
| `10.1.0.0/16` | Peering Connection → `vpc-a-to-vpc-b` |

### Update Route Table for VPC-B

Add the following route:

| Destination   | Target                                |
| ------------- | ------------------------------------- |
| `10.0.0.0/16` | Peering Connection → `vpc-a-to-vpc-b` |

> **Note**
>
> Route tables in both VPCs must include routes to each other's CIDR blocks for bidirectional communication.

---

# Step 5 – Update Security Groups

Update the security group associated with the EC2 instance in **vpc-b**.

Add the following inbound rule:

| Type            | Source        |
| --------------- | ------------- |
| All ICMP – IPv4 | `10.0.0.0/16` |

> **Important**
>
> ICMP traffic must be allowed to successfully test connectivity using the `ping` command.

---

# Step 6 – Test Peering

Connect to the EC2 instance in **VPC-A** using SSH.

Run the following command to ping the private IP address of the EC2 instance in **VPC-B**.

```bash
ping 10.1.1.x
```

Expected result:

```text
Should get responses — VPCs are now peered!
```

> **Success**
>
> Successful ping responses confirm that the VPC Peering Connection is functioning correctly and that both VPCs can communicate using private IP addresses.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Second VPC created successfully
* ✅ Subnet created in `vpc-b`
* ✅ EC2 instance launched in `vpc-b`
* ✅ VPC Peering Connection created
* ✅ Peering request accepted
* ✅ Route tables updated in both VPCs
* ✅ Security Group updated to allow ICMP traffic
* ✅ Ping test successful using private IP addresses

---

# Best Practices

* 💡 Use non-overlapping CIDR blocks when creating VPCs for peering.
* 💡 Update route tables in both VPCs to enable bidirectional communication.
* 💡 Restrict security group rules to only the required CIDR ranges.
* 💡 Use VPC Peering for low-latency communication between VPCs in the same or different AWS accounts.
* 💡 Regularly review peering connections and route tables to maintain a secure network architecture.

---

# Troubleshooting

| Issue                              | Possible Cause                                   | Resolution                                                                                |
| ---------------------------------- | ------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| Peering connection remains pending | Request not accepted                             | Accept the peering request from the accepter VPC.                                         |
| Ping requests fail                 | ICMP blocked by the Security Group               | Add an inbound **All ICMP – IPv4** rule for the appropriate CIDR block.                   |
| Instances cannot communicate       | Missing route table entries                      | Verify that both route tables include routes to the peer VPC CIDR blocks.                 |
| Incorrect destination CIDR         | Overlapping or misconfigured CIDR blocks         | Ensure the VPC CIDR ranges do not overlap and the correct destination CIDR is configured. |
| Private communication unavailable  | EC2 instance not running or incorrect private IP | Verify the instance is running and use the correct private IP address for testing.        |
