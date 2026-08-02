# 1.13 AWS Migration

# Lab 29: Database Migration to AWS (DMS)

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 45 Minutes

---

# Overview

In this lab, you will migrate a MySQL database from a simulated **on-premises environment** to **Amazon RDS for MySQL** using **AWS Database Migration Service (AWS DMS)**.

The lab simulates a real-world database migration scenario where:

* A source MySQL database runs on an EC2 instance (simulating an on-premises server).
* AWS DMS performs the migration.
* Amazon RDS MySQL acts as the target database.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create a simulated on-premises MySQL source database
* ✅ Configure an Amazon RDS MySQL target database
* ✅ Create an AWS DMS replication instance
* ✅ Configure source and target endpoints
* ✅ Run a database migration task
* ✅ Verify migrated data in Amazon RDS

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* IAM permissions for:

  * AWS Database Migration Service
  * Amazon RDS
  * Amazon EC2
* EC2 instance for source database simulation
* MySQL client installed
* Network connectivity between source and target databases

---

# Migration Architecture Overview

```text id="m9q4vx"
              Simulated On-Premises Environment

                     EC2 Instance
                         │
                         │
                  MySQL Source DB
                     sourcedb
                         │
                         │
                         ▼

                AWS DMS Replication Instance
                    devops-dms
                         │
                         │
                         ▼

                 Amazon RDS MySQL
                 migration-target

                  Database: targetdb
```

---

# Step 1 – Set Up Source Database (Simulate On-Prem)

Launch an EC2 instance and install MySQL.

---

## Install MySQL Server

Run:

```bash id="x7m2qp"
sudo apt update
sudo apt install mysql-server -y
```

Access MySQL:

```bash id="v8k4ms"
sudo mysql
```

---

## Create Source Database

Execute:

```sql id="p5n8qw"
CREATE DATABASE sourcedb;

USE sourcedb;

CREATE TABLE products (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(100),
price DECIMAL(10,2)
);
```

Insert sample data:

```sql id="k9m3vz"
INSERT INTO products VALUES
(NULL,'Laptop',75000),
(NULL,'Phone',25000),
(NULL,'Tablet',40000);
```

Verify data:

```sql id="r4x7mp"
SELECT * FROM products;
```

Exit MySQL:

```sql id="w6q2ns"
EXIT;
```

Expected result:

```text id="z3m8vk"
Products table contains 3 records
```

---

# Step 2 – Create Target RDS MySQL Database

📍 **Navigation**

```text id="h7p3mq"
RDS → Create database
```

Configure:

| Setting         | Value              |
| --------------- | ------------------ |
| Engine          | MySQL 8.0          |
| Template        | Free tier          |
| Identifier      | `migration-target` |
| Master Username | `admin`            |
| Password        | `Admin@2024!`      |
| Public Access   | Yes                |

Create the database.

> **Note**
>
> This RDS instance will receive the migrated database objects and data.

---

# Step 3 – Create DMS Replication Instance

📍 **Navigation**

```text id="q5v8mx"
DMS → Replication instances → Create
```

Configure:

| Setting        | Value          |
| -------------- | -------------- |
| Name           | `devops-dms`   |
| Instance Class | `dms.t3.micro` |
| VPC            | Default        |
| Multi-AZ       | No (Lab)       |

Click:

```text
Create
```

Wait approximately:

```text
5 minutes
```

for the replication instance to become available.

---

# Step 4 – Create Source Endpoint

📍 **Navigation**

```text id="n8m4kp"
DMS → Endpoints → Create
```

Configure:

| Setting       | Value               |
| ------------- | ------------------- |
| Endpoint Type | Source              |
| Engine        | MySQL               |
| Server        | EC2 Private IP      |
| Port          | 3306                |
| Username      | root                |
| Password      | Your MySQL password |
| Database      | `sourcedb`          |

Test the connection:

```text
Test connection → Successful
```

---

# Step 5 – Create Target Endpoint

Create the RDS destination endpoint.

Configure:

| Setting       | Value         |
| ------------- | ------------- |
| Endpoint Type | Target        |
| Engine        | MySQL         |
| Server        | RDS Endpoint  |
| Port          | 3306          |
| Username      | admin         |
| Password      | `Admin@2024!` |
| Database      | `targetdb`    |

Test the connection:

```text
Test connection → Successful
```

> **Note**
>
> The target endpoint connects AWS DMS to the Amazon RDS MySQL database.

---

# Step 6 – Create and Run Migration Task

📍 **Navigation**

```text id="t4m8qx"
DMS → Database migration tasks → Create
```

Configure:

| Setting              | Value                 |
| -------------------- | --------------------- |
| Task Identifier      | `products-migration`  |
| Replication Instance | `devops-dms`          |
| Source Endpoint      | Source MySQL          |
| Target Endpoint      | Target RDS            |
| Migration Type       | Migrate existing data |

---

## Table Mapping

Configure:

```text id="c7m2vx"
Schema:
sourcedb
```

Enable:

```text id="j8q4mp"
Start task on create: Checked
```

Create the migration task.

Monitor:

```text id="y5m9vk"
DMS → Migration Tasks → Progress
```

---

# Step 7 – Verify Migration

Connect to the RDS MySQL database from EC2.

```bash id="p3x8mq"
mysql -h <RDS_ENDPOINT> -u admin -p
```

View databases:

```sql id="n6q2wp"
SHOW DATABASES;
```

Select the migrated database:

```sql id="m8k4vz"
USE targetdb;
```

Verify migrated data:

```sql id="x4q9ms"
SELECT * FROM products;
```

Expected result:

```text id="r7m2kp"
Laptop   75000
Phone    25000
Tablet   40000
```

> **Success**
>
> The MySQL database has been successfully migrated from the simulated on-premises environment to Amazon RDS using AWS DMS.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Source MySQL database created
* ✅ Sample product data inserted
* ✅ RDS MySQL target database created
* ✅ DMS replication instance created
* ✅ Source endpoint connection successful
* ✅ Target endpoint connection successful
* ✅ Migration task completed successfully
* ✅ Data verified in RDS MySQL

---

# Best Practices

* 💡 Test migrations in a staging environment before production migration.
* 💡 Use AWS DMS validation features to compare source and target data.
* 💡 Monitor replication latency during migration.
* 💡 Use encryption for database connections.
* 💡 Configure appropriate security groups between DMS, source, and target databases.
* 💡 Plan downtime windows carefully for production database migrations.
* 💡 Perform backups before starting migration activities.

---

# Troubleshooting

| Issue                            | Possible Cause              | Resolution                                                    |
| -------------------------------- | --------------------------- | ------------------------------------------------------------- |
| Source endpoint connection fails | MySQL port blocked          | Allow inbound MySQL traffic on port `3306`.                   |
| DMS cannot connect to EC2        | Security group restrictions | Allow DMS replication instance access to the source database. |
| Target endpoint test fails       | Incorrect RDS credentials   | Verify RDS endpoint, username, and password.                  |
| Migration task fails             | Incorrect table mapping     | Review schema and table mapping configuration.                |
| Data missing after migration     | Migration not completed     | Check DMS task logs and migration status.                     |
