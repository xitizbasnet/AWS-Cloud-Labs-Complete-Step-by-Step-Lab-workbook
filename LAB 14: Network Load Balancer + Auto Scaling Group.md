# LAB 14: Network Load Balancer + Auto Scaling Group

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 60 Minutes

---

# Overview

In this lab, you will configure a highly available and scalable web application infrastructure using an **Amazon EC2 Launch Template**, **Auto Scaling Group (ASG)**, and **Network Load Balancer (NLB)**.

By the end of this lab, multiple EC2 instances will automatically launch, register with the load balancer, and scale based on CPU utilization.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an EC2 Launch Template
* ✅ Configure Apache installation using EC2 User Data
* ✅ Create a Target Group
* ✅ Deploy a Network Load Balancer (NLB)
* ✅ Create an Auto Scaling Group (ASG)
* ✅ Configure CPU-based Auto Scaling
* ✅ Validate Load Balancing
* ✅ Test automatic scaling using CPU stress

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Amazon EC2 Key Pair (`linux-key`)
* Default VPC available
* Security Group allowing:

  * HTTP (Port **80**)
  * SSH (Port **22**)

---

# Architecture Overview

```text
                    Internet
                        │
                        ▼
          Network Load Balancer (TCP:80)
                        │
               Target Group (HTTP:80)
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼                               ▼
   EC2 Instance 1                  EC2 Instance 2
        │                               │
        └──────── Auto Scaling Group ───┘
                  (Min:1 Max:4 Desired:2)
```

---

# Step 1 – Create Launch Template

📍 **Navigation**

```text
EC2 → Launch Templates → Create launch template
```

Configure the following settings:

| Setting          | Value                 |
| ---------------- | --------------------- |
| Name             | `webserver-template`  |
| AMI              | Amazon Linux 2023     |
| Instance type    | `t2.micro`            |
| Key pair         | `linux-key`           |
| Security Group   | Allow HTTP 80, SSH 22 |
| Advanced Details | Add User Data script  |

---

# Step 2 – User Data Script

📍 **Advanced Details → User Data**

Use the following script to automatically install and configure Apache when the EC2 instance launches.

```bash
#!/bin/bash
yum update -y
yum install httpd -y
systemctl start httpd
systemctl enable httpd
echo "<h1>Server: $(hostname)</h1>" > /var/www/html/index.html
```

> **Note**
>
> The User Data script executes during the first boot of the EC2 instance. It installs Apache HTTP Server, starts the service, enables it at startup, and creates a simple webpage displaying the server hostname.

---

# Step 3 – Create Target Group

📍 **Navigation**

```text
EC2 → Target Groups → Create target group
```

Configure the following settings:

| Setting           | Value          |
| ----------------- | -------------- |
| Target type       | Instances      |
| Target group name | `webserver-tg` |
| Protocol          | HTTP           |
| Port              | 80             |
| VPC               | Default        |
| Health check path | `/`            |

After configuring the settings:

* Click **Next**
* Click **Create target group**

> **Important**
>
> Do **not** register any instances at this stage. The Auto Scaling Group will automatically register instances with the target group.

---

# Step 4 – Create Network Load Balancer

📍 **Navigation**

```text
EC2 → Load Balancers → Create load balancer → Network Load Balancer
```

Configure the following settings:

| Setting            | Value           |
| ------------------ | --------------- |
| Name               | `webserver-nlb` |
| Scheme             | Internet-facing |
| IP address type    | IPv4            |
| VPC                | Default         |
| Availability Zones | Select all AZs  |
| Listener           | TCP Port 80     |
| Forward to         | `webserver-tg`  |

After completing the configuration:

* Click **Create load balancer**

---

# Step 5 – Create Auto Scaling Group

📍 **Navigation**

```text
EC2 → Auto Scaling Groups → Create Auto Scaling group
```

Configure the following settings:

| Setting                 | Value                     |
| ----------------------- | ------------------------- |
| Name                    | `webserver-asg`           |
| Launch template         | `webserver-template`      |
| VPC                     | Default                   |
| Subnets                 | Select all subnets        |
| Attach to load balancer | `webserver-tg`            |
| Health checks           | Turn on ELB health checks |
| Desired capacity        | 2                         |
| Minimum capacity        | 1                         |
| Maximum capacity        | 4                         |
| Scaling policy          | Target tracking           |
| Target CPU utilization  | 50%                       |

After completing the configuration:

* Click **Next**
* Click **Create Auto Scaling group**

---

# Step 6 – Test Load Balancer

Allow approximately **3–5 minutes** for the Auto Scaling Group to launch EC2 instances and register them with the Target Group.

📍 **Navigation**

```text
EC2 → Load Balancers
```

Perform the following steps:

1. Copy the **Network Load Balancer DNS Name**.
2. Open the DNS name in a web browser.
3. Refresh the page multiple times.

Expected result:

* The displayed hostname changes between requests.
* Requests are distributed across different EC2 instances by the Network Load Balancer.

> **Success**
>
> Changing hostnames indicate that traffic is being successfully load balanced between multiple servers.

---

# Step 7 – Test Auto Scaling

SSH into one of the EC2 instances and generate CPU load.

Run the following commands:

```bash
sudo yum install stress -y
stress --cpu 4 --timeout 300 &
```

Monitor the Auto Scaling Group:

```text
EC2 → Auto Scaling Groups → Activity
```

Observe the following:

* New EC2 instances launch automatically.
* The instance count increases beyond the desired capacity of **2** as CPU utilization exceeds the configured target.

> **Success**
>
> The Auto Scaling Group automatically provisions additional EC2 instances to maintain the target CPU utilization of **50%**.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Launch Template created successfully
* ✅ Apache installed using User Data
* ✅ Target Group created
* ✅ Network Load Balancer created
* ✅ Auto Scaling Group created
* ✅ Desired capacity set to **2**
* ✅ Load Balancer distributes traffic between EC2 instances
* ✅ CPU stress test triggers Auto Scaling
* ✅ Additional EC2 instances launch automatically

---

# Best Practices

* 💡 Use Launch Templates for standardized EC2 deployments.
* 💡 Enable ELB health checks to improve instance replacement accuracy.
* 💡 Configure Auto Scaling policies based on application performance metrics.
* 💡 Distribute instances across multiple Availability Zones for high availability.
* 💡 Regularly monitor Auto Scaling activities using Amazon CloudWatch.

---

# Troubleshooting

| Issue                                              | Possible Cause                               | Resolution                                                                                                    |
| -------------------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Apache page is not accessible                      | Apache service not running                   | Verify the User Data script and confirm the `httpd` service is active.                                        |
| Target Group health check fails                    | Security Group or health check configuration | Ensure HTTP (Port 80) is allowed and the health check path is `/`.                                            |
| Load Balancer displays only one hostname           | Only one healthy instance is registered      | Verify that multiple EC2 instances are running and healthy in the Target Group.                               |
| Auto Scaling does not launch additional instances  | CPU utilization remains below threshold      | Confirm the `stress` utility is running and review CloudWatch CPU metrics.                                    |
| Instances are not registered with the Target Group | Auto Scaling Group configuration issue       | Verify that the Auto Scaling Group is attached to the correct Target Group and ELB health checks are enabled. |
