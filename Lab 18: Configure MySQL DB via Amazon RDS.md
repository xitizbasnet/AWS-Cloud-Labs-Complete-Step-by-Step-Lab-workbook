# 1.7 AWS Database Services

# Lab 18: Configure MySQL DB via Amazon RDS

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 45 Minutes

---

# Overview

In this lab, you will deploy a **MySQL database** using **Amazon RDS**, configure network access, connect to the database from an Amazon EC2 instance, create a database table, insert sample records, and create an RDS snapshot for backup and recovery.

By the end of this lab, you will have a fully functional managed MySQL database hosted on Amazon RDS.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an Amazon RDS MySQL instance
* ✅ Configure RDS security group access
* ✅ Connect to the database from an EC2 instance
* ✅ Create a database table
* ✅ Insert and retrieve records
* ✅ Create an RDS snapshot for backup
* ✅ Understand basic backup and restore operations

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Running Amazon EC2 instance
* MySQL client installed (or install during the lab)
* Network connectivity between the EC2 instance and Amazon RDS
* Permissions to access:

  * Amazon RDS
  * Amazon EC2
  * Amazon VPC
  * Security Groups

---

# Architecture Overview

```text
                 Amazon EC2
             (MySQL Client Installed)
                      │
              MySQL Port (3306)
                      │
                      ▼
            Amazon RDS MySQL Instance
             Database: devopsdb
                      │
                      ▼
               RDS Snapshot Backup
```

---

# Step 1 – Create RDS MySQL Instance

📍 **Navigation**

```text
RDS → Create database
```

Configure the following settings:

| Setting                | Value                                   |
| ---------------------- | --------------------------------------- |
| Creation method        | Standard create                         |
| Engine                 | MySQL                                   |
| Version                | 8.0                                     |
| Template               | Free tier                               |
| DB instance identifier | `devops-mysql`                          |
| Master username        | `admin`                                 |
| Master password        | `Admin@2024!`                           |
| DB instance class      | `db.t3.micro` (Free tier)               |
| Storage                | 20 GB gp2                               |
| Public access          | Yes (for lab)                           |
| Security Group         | Create new VPC security group: `rds-sg` |
| Initial database name  | `devopsdb`                              |

After completing the configuration:

* Click **Create database**

> **Note**
>
> Database provisioning typically takes **5–10 minutes**.

---

# Step 2 – Configure Security Group

While the RDS instance is being created, update the associated security group.

Edit the **`rds-sg`** security group and add the following inbound rule:

| Setting | Value                                    |
| ------- | ---------------------------------------- |
| Type    | MySQL/Aurora (3306)                      |
| Source  | EC2 Security Group **or** Your Public IP |

After completing the configuration:

* Click **Save rules**

> **Important**
>
> Restrict inbound access to only the required EC2 security group or your trusted public IP. Avoid allowing unrestricted (`0.0.0.0/0`) access in production environments.

---

# Step 3 – Connect from EC2

Connect to your EC2 instance using SSH.

Install the MySQL client.

```bash
sudo yum install mysql -y
```

Retrieve the **RDS Endpoint** from the AWS Management Console.

📍 **Navigation**

```text
RDS → Databases → devops-mysql → Connectivity & Security
```

Connect to the RDS instance.

```bash
mysql -h <RDS_ENDPOINT> -u admin -p
```

When prompted, enter the password:

```text
Admin@2024!
```

> **Success**
>
> You are now connected to the Amazon RDS MySQL instance.

---

# Step 4 – Create Database and Tables

Run the following SQL commands.

```sql
SHOW DATABASES;

USE devopsdb;

CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    role VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO employees (name, role) VALUES
('Vinod Muleva', 'DevOps Engineer'),
('Rahul Sharma', 'Cloud Architect');

SELECT * FROM employees;

EXIT;
```

Expected outcome:

* The `employees` table is created.
* Two records are inserted successfully.
* The `SELECT` statement displays the inserted records.

---

# Step 5 – Create RDS Snapshot (Backup)

📍 **Navigation**

```text
RDS → Databases → Select your DB
```

Perform the following steps:

1. Select the database instance.
2. Click **Actions**.
3. Select **Take snapshot**.
4. Enter the snapshot name:

```text
devops-mysql-backup
```

5. Click **Take snapshot**.

To restore the snapshot:

```text
RDS → Snapshots → Select Snapshot → Restore Snapshot
```

> **Success**
>
> The snapshot provides a point-in-time backup that can be restored to create a new RDS database instance.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Amazon RDS MySQL instance created successfully
* ✅ Security Group configured to allow MySQL (3306) access
* ✅ Connected to the database from the EC2 instance
* ✅ `devopsdb` database accessible
* ✅ `employees` table created
* ✅ Sample records inserted successfully
* ✅ Data verified using the `SELECT` statement
* ✅ RDS snapshot created successfully

---

# Best Practices

* 💡 Use private database instances whenever possible; enable **Public Access** only for lab or testing environments.
* 💡 Restrict MySQL access to trusted security groups or IP addresses.
* 💡 Use strong passwords and consider **AWS Secrets Manager** for credential management.
* 💡 Schedule automated backups and retain manual snapshots before making significant changes.
* 💡 Enable Multi-AZ deployments for production workloads to improve availability.
* 💡 Monitor database performance using **Amazon CloudWatch** and **Amazon RDS Performance Insights**.

---

# Troubleshooting

| Issue                                 | Possible Cause                                     | Resolution                                                                              |
| ------------------------------------- | -------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Unable to connect to the RDS instance | Security Group does not allow MySQL traffic        | Verify that port **3306** is open to the EC2 security group or your public IP.          |
| Connection timed out                  | Incorrect RDS endpoint or networking configuration | Confirm the RDS endpoint, VPC configuration, and security group settings.               |
| Access denied for user                | Incorrect username or password                     | Verify the credentials (`admin` / `Admin@2024!`) entered during database creation.      |
| MySQL client not found                | MySQL client package not installed                 | Install the client using `sudo yum install mysql -y`.                                   |
| Snapshot creation fails               | Database instance not in an available state        | Wait until the RDS instance status is **Available**, then retry the snapshot operation. |
