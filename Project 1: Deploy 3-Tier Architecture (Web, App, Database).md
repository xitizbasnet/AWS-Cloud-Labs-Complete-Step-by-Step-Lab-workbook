# SECTION 2 — Real-Time Projects

# Project 1: Deploy 3-Tier Architecture (Web, App, Database)

> **Project Level:** 🔴 Expert
> **Architecture Type:** Production-Style Multi-Tier Application
> **Components:** Web Tier + Application Tier + Database Tier

---

# Overview

In this real-time project, you will deploy a highly available **3-Tier Architecture** on AWS.

The architecture separates application components into three independent layers:

1. **Web Tier**

   * Amazon EC2
   * Application Load Balancer (ALB)
   * Auto Scaling
   * Public Subnets

2. **Application Tier**

   * Amazon EC2
   * Flask API Application
   * Private Subnets

3. **Database Tier**

   * Amazon RDS MySQL
   * Private Subnets
   * No Internet Access

This architecture follows AWS security and scalability best practices by isolating each application layer.

---

# Architecture Overview

```text
                         Internet Users
                              │
                              ▼
                    Application Load Balancer
                              │
              ┌───────────────┴───────────────┐
              │                               │
              ▼                               ▼

        Web Tier EC2                    Web Tier EC2
        Public Subnet                   Public Subnet
              │                               │
              └───────────────┬───────────────┘
                              │
                              ▼

                    Application Tier EC2
                    Private Subnet
                    Flask API :5000
                              │
                              ▼

                    Database Tier
                    RDS MySQL
                    Private Subnet

```

---

# Network Design

## VPC Configuration

Create a custom VPC:

| Component        | Configuration   |
| ---------------- | --------------- |
| VPC CIDR         | `10.0.0.0/16`   |
| Internet Gateway | Attached to VPC |
| NAT Gateway      | Public subnet   |
| Region           | `ap-south-1`    |

---

# Subnet Design

| Tier     | Subnet Name      | CIDR          | Availability Zone |
| -------- | ---------------- | ------------- | ----------------- |
| Web Tier | Public Subnet 1  | `10.0.1.0/24` | `ap-south-1a`     |
| Web Tier | Public Subnet 2  | `10.0.2.0/24` | `ap-south-1b`     |
| App Tier | Private Subnet 1 | `10.0.3.0/24` | `ap-south-1a`     |
| DB Tier  | Private Subnet 2 | `10.0.4.0/24` | `ap-south-1b`     |

---

# Step 1 — Architecture Design

## Create Custom VPC

Configure:

```text
CIDR:
10.0.0.0/16
```

---

## Create Public Subnets

### Public Subnet 1

```text
Name:
public-subnet-1

CIDR:
10.0.1.0/24

Availability Zone:
ap-south-1a
```

Purpose:

```text
Web Tier
```

---

### Public Subnet 2

```text
Name:
public-subnet-2

CIDR:
10.0.2.0/24

Availability Zone:
ap-south-1b
```

Purpose:

```text
Web Tier
```

---

## Create Private Subnets

### Private Subnet 1

```text
Name:
private-subnet-app

CIDR:
10.0.3.0/24

Availability Zone:
ap-south-1a
```

Purpose:

```text
Application Tier
```

---

### Private Subnet 2

```text
Name:
private-subnet-db

CIDR:
10.0.4.0/24

Availability Zone:
ap-south-1b
```

Purpose:

```text
Database Tier
```

---

## Configure Internet Gateway

Create and attach:

```text
Internet Gateway → VPC
```

Purpose:

* Allows public subnet resources to communicate with the internet.

---

## Configure NAT Gateway

Create NAT Gateway:

```text
Location:
Public Subnet
```

Purpose:

* Allows private EC2 instances to access the internet for updates.
* Prevents inbound internet access to private resources.

---

# Step 2 — Create Security Groups

Create separate security groups for each architecture layer.

---

# Web Security Group

Name:

```text
web-sg
```

Inbound Rules:

| Protocol | Port | Source      |
| -------- | ---- | ----------- |
| HTTP     | 80   | `0.0.0.0/0` |
| HTTPS    | 443  | `0.0.0.0/0` |

Purpose:

```text
Allow internet users to access web servers.
```

---

# Application Security Group

Name:

```text
app-sg
```

Inbound Rules:

| Protocol | Port | Source   |
| -------- | ---- | -------- |
| TCP      | 5000 | `web-sg` |

Purpose:

```text
Allow only web tier traffic.
```

---

# Database Security Group

Name:

```text
db-sg
```

Inbound Rules:

| Protocol | Port | Source   |
| -------- | ---- | -------- |
| MySQL    | 3306 | `app-sg` |

Purpose:

```text
Allow database access only from application servers.
```

---

# Step 3 — Deploy Database Tier

Create Amazon RDS MySQL database.

📍 **Navigation**

```text
RDS → Create Database
```

Configure:

| Setting        | Value           |
| -------------- | --------------- |
| Engine         | MySQL           |
| Template       | Free Tier       |
| VPC            | `devops-vpc`    |
| Subnet Group   | Private Subnets |
| Security Group | `db-sg`         |
| Public Access  | No              |
| Database Name  | `appdb`         |

Create database.

---

## Database Security Model

```text
Internet
   ❌
   │
   ▼
RDS MySQL
(private subnet)

Allowed:
App Tier → RDS
```

---

# Step 4 — Deploy Application Tier

Launch EC2 instance:

| Setting        | Value              |
| -------------- | ------------------ |
| Subnet         | `private-subnet-1` |
| Security Group | `app-sg`           |

---

## User Data Script

```bash
#!/bin/bash

apt update -y
apt install python3-pip -y

pip3 install flask pymysql

cat > /home/ubuntu/app.py << 'EOF'

from flask import Flask, jsonify
import pymysql

app = Flask(__name__)

@app.route('/api/health')
def health():
    return jsonify({
        'status':'healthy',
        'tier':'app'
    })

@app.route('/api/data')
def data():

    conn = pymysql.connect(
        host='<RDS_ENDPOINT>',
        user='admin',
        password='Admin@2024!',
        database='appdb'
    )

    return jsonify({
        'message':'Data from DB',
        'tier':'app'
    })

if __name__ == '__main__':
    app.run(
        host='0.0.0.0',
        port=5000
    )

EOF

nohup python3 /home/ubuntu/app.py &
```

---

# Step 5 — Deploy Web Tier with ALB

Launch EC2 instance:

| Setting        | Value           |
| -------------- | --------------- |
| Subnet         | Public Subnet 1 |
| Security Group | `web-sg`        |

---

## Install Nginx Web Server

User Data:

```bash
#!/bin/bash

apt update -y
apt install nginx -y

cat > /etc/nginx/sites-available/default << 'EOF'

server {

listen 80;

location / {

proxy_pass http://<APP_PRIVATE_IP>:5000;

}

}

EOF

nginx -t

systemctl restart nginx
```

---

# Create Application Load Balancer

📍 **Navigation**

```text
EC2 → Load Balancers → Create Load Balancer
```

Configure:

```text
Type:
Application Load Balancer
```

Settings:

* Internet-facing
* Public subnets
* HTTP listener port 80

---

## Target Group

Create target group:

```text
Target Type:
Instances
```

Register:

```text
Web EC2 Instances
```

Configure:

```text
ALB
   ↓
Web Tier EC2
   ↓
Nginx Proxy
   ↓
App Tier Flask API
```

---

# Step 6 — Test Full Architecture

Open browser:

```text
http://<ALB_DNS>/api/health
```

Expected response:

```json
{
 "status": "healthy",
 "tier": "app"
}
```

---

# Complete Traffic Flow

```text
User
 │
 ▼
Application Load Balancer
 │
 ▼
Web Nginx EC2
 │
 ▼
Flask Application EC2
 │
 ▼
RDS MySQL
```

---

# Security Validation

Verify:

✅ Web servers are publicly accessible
✅ Application servers have no public IP
✅ Database has no public access
✅ Only Web Tier can access App Tier
✅ Only App Tier can access Database Tier
✅ Traffic flows through ALB

---

# Project Completion Checklist

* ✅ Custom VPC created
* ✅ Public and private subnet architecture implemented
* ✅ Internet Gateway configured
* ✅ NAT Gateway configured
* ✅ Security groups created
* ✅ RDS MySQL deployed privately
* ✅ Flask application deployed privately
* ✅ Nginx web tier deployed
* ✅ ALB configured
* ✅ End-to-end application flow tested

---

# Production Enhancement Recommendations

* 💡 Add Auto Scaling Groups for Web and App tiers.
* 💡 Deploy Web Tier across multiple Availability Zones.
* 💡 Enable RDS Multi-AZ deployment.
* 💡 Store database credentials in AWS Secrets Manager.
* 💡 Enable CloudWatch monitoring and alarms.
* 💡 Add AWS WAF protection in front of ALB.
* 💡 Enable HTTPS using AWS Certificate Manager (ACM).
* 💡 Use Infrastructure as Code with AWS CloudFormation or Terraform.
