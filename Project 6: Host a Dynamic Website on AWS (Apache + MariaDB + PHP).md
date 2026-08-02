# SECTION 2 — Real-Time Projects

# Project 6: Host a Dynamic Website on AWS (Apache + MariaDB + PHP)

> **Project Level:** 🔴 Advanced
> **Architecture Type:** Dynamic Web Application Hosting
> **Technology Stack:** Apache HTTP Server + PHP + MariaDB + Amazon EC2

---

# Overview

In this real-time project, you will deploy a dynamic website on AWS using a traditional **LAMP stack** architecture.

The project demonstrates how to host a PHP-based web application with database connectivity:

* Amazon EC2 hosts the web server and application
* Apache serves PHP pages
* MariaDB stores application data
* PHP connects the frontend application with the database

The final application will provide a contact form where users can submit information, which will be stored in the MariaDB database.

---

# Architecture Overview

```text id="x1b8hc"
                 User Browser

                      │

                      ▼

                 HTTP Request
                 Port 80

                      │

                      ▼

                Amazon EC2 Instance

                      │

          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼

      Apache HTTPD              PHP

          │                       │

          └───────────┬───────────┘

                      │

                      ▼

                 MariaDB Database

                    myapp
```

---

# Learning Objectives

After completing this project, you will be able to:

* ✅ Launch an EC2 instance for web hosting
* ✅ Install and configure Apache web server
* ✅ Install PHP runtime and database extensions
* ✅ Configure MariaDB database services
* ✅ Create database users and permissions
* ✅ Build a PHP application connected to MariaDB
* ✅ Test a dynamic website hosted on AWS

---

# Prerequisites

Before starting this project, ensure you have:

* AWS Account
* EC2 access permissions
* SSH key pair
* Security group configuration
* Basic Linux command knowledge

---

# Technology Components

| Component            | Technology         |
| -------------------- | ------------------ |
| Compute              | Amazon EC2         |
| Web Server           | Apache HTTP Server |
| Application Language | PHP 8.2            |
| Database             | MariaDB            |
| Operating System     | Amazon Linux 2023  |

---

# Step 1 — Launch EC2 for LAMP Stack

📍 **Navigation**

```text id="z2n8pk"
EC2 → Launch Instance
```

---

# Instance Configuration

Configure:

| Setting        | Value             |
| -------------- | ----------------- |
| AMI            | Amazon Linux 2023 |
| Instance Type  | `t2.micro`        |
| Security Group | SSH + HTTP        |

---

# Security Group Rules

Configure inbound access:

| Type | Port | Source      |
| ---- | ---- | ----------- |
| SSH  | 22   | Your IP     |
| HTTP | 80   | `0.0.0.0/0` |

---

Launch the instance.

---

# Step 2 — Install LAMP Stack

Connect to EC2:

```bash id="q9k3vm"
ssh -i linux-key.pem ec2-user@<IP>
```

---

# Update System Packages

```bash id="s8k4nt"
sudo dnf update -y
```

---

# Install Required Packages

Install:

* Apache HTTP Server
* MariaDB Server
* PHP
* MySQL PHP extension

Run:

```bash id="m5q7vx"
sudo dnf install httpd mariadb105-server php8.2 php8.2-mysqlnd -y
```

---

# Start Services

Start Apache and MariaDB:

```bash id="p3w8cz"
sudo systemctl start httpd mariadb
```

Enable services on boot:

```bash id="h7x2mq"
sudo systemctl enable httpd mariadb
```

---

# Verify Services

Check Apache:

```bash id="v4n9ks"
systemctl status httpd
```

Check MariaDB:

```bash id="c8m3px"
systemctl status mariadb
```

---

# Step 3 — Configure MariaDB

Run the security configuration:

```bash id="r6y1qw"
sudo mysql_secure_installation
```

---

# MariaDB Security Configuration

Configure:

| Option                    | Value          |
| ------------------------- | -------------- |
| Root Password             | `DBRoot@2024!` |
| Remove Anonymous Users    | Yes            |
| Disable Remote Root Login | Yes            |
| Remove Test Database      | Yes            |
| Reload Privileges         | Yes            |

---

# Login to MariaDB

```bash id="t8m4qp"
sudo mysql -u root -p
```

Enter:

```text id="b2v9mx"
DBRoot@2024!
```

---

# Create Application Database

Create database:

```sql id="f9k2mw"
CREATE DATABASE myapp;
```

---

# Create Application Database User

Create user:

```sql id="n7x3qp"
CREATE USER 'appuser'@'localhost'
IDENTIFIED BY 'App@2024!';
```

---

# Grant Permissions

```sql id="p6m8vz"
GRANT ALL ON myapp.* 
TO 'appuser'@'localhost';
```

Apply changes:

```sql id="x5q1nr"
FLUSH PRIVILEGES;
```

---

# Create Application Table

Select database:

```sql id="h3v7mk"
USE myapp;
```

Create contacts table:

```sql id="w8p4sx"
CREATE TABLE contacts (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(100),
email VARCHAR(100),
message TEXT,
created TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Exit:

```sql id="z6n2qp"
EXIT;
```

---

# Step 4 — Create PHP Application

Create PHP file:

```bash id="a7m3qw"
sudo nano /var/www/html/index.php
```

Add:

```php id="g9x2mv"
<?php

$conn = new mysqli(
    'localhost',
    'appuser',
    'App@2024!',
    'myapp'
);

if($conn->connect_error)
    die('Connection failed');

if($_POST){

    $stmt = $conn->prepare(
        'INSERT INTO contacts(name,email,message)
         VALUES(?,?,?)'
    );

    $stmt->bind_param(
        'sss',
        $_POST['name'],
        $_POST['email'],
        $_POST['message']
    );

    $stmt->execute();

    echo '<p>Message sent!</p>';
}

?>

<h1>Contact Us</h1>

<form method="POST">

Name:
<input name="name">
<br>

Email:
<input name="email">
<br>

Message:
<textarea name="message"></textarea>
<br>

<input type="submit" value="Send">

</form>
```

---

# Application Flow

```text id="w4z9pk"
User submits form

        │

        ▼

PHP Application

        │

        ▼

MySQL Connection

        │

        ▼

MariaDB contacts table

        │

        ▼

Data Stored
```

---

# Step 5 — Test the Dynamic Application

Open browser:

```text id="x9m3kv"
http://<EC2_IP>/index.php
```

---

# Submit Contact Form

Enter:

```text id="q2v7mz"
Name:
Your Name

Email:
your@email.com

Message:
Hello AWS
```

Click:

```text id="n8p4cx"
Send
```

Expected result:

```text id="k5x9qw"
Message sent!
```

---

# Verify Database Records

Login:

```bash id="m4v8zp"
sudo mysql -u root -p
```

Select database:

```sql id="s2n6kx"
USE myapp;
```

View submitted data:

```sql id="j7q3mw"
SELECT * FROM contacts;
```

Expected output:

```text id="r5v8nx"
id | name | email | message | created
```

---

# Complete Application Flow

```text id="c7m1qx"
Browser

   │

   ▼

Apache Web Server

   │

   ▼

PHP Application

   │

   ▼

MariaDB Database

   │

   ▼

contacts Table
```

---

# Validation Checklist

Verify:

* ✅ EC2 instance launched successfully
* ✅ Apache installed and running
* ✅ MariaDB installed and configured
* ✅ PHP runtime installed
* ✅ Database created
* ✅ Application user created
* ✅ PHP application deployed
* ✅ Contact form loads successfully
* ✅ Form submissions stored in database

---

# Production Best Practices

* 💡 Use Amazon RDS instead of MariaDB running on EC2 for production databases.
* 💡 Place the database in a private subnet.
* 💡 Use HTTPS with AWS Certificate Manager.
* 💡 Store database credentials in AWS Secrets Manager.
* 💡 Use Auto Scaling for high availability.
* 💡 Place a Load Balancer in front of web servers.
* 💡 Enable CloudWatch monitoring and application logs.
* 💡 Regularly back up database data.

---

# Troubleshooting

| Issue                                 | Possible Cause          | Resolution                                  |
| ------------------------------------- | ----------------------- | ------------------------------------------- |
| Website not loading                   | Apache service stopped  | Start Apache using `systemctl start httpd`. |
| PHP code displayed instead of running | PHP module missing      | Verify PHP installation and restart Apache. |
| Database connection failed            | Incorrect credentials   | Verify database username/password.          |
| Form submission fails                 | Table missing           | Confirm `contacts` table exists.            |
| Cannot access website                 | Security group issue    | Allow inbound HTTP port 80.                 |
| MariaDB login fails                   | Incorrect root password | Reconfigure MariaDB credentials.            |
