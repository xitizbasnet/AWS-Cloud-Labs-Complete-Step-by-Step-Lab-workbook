# 1.4 AWS Elastic Compute Cloud (EC2)

# Lab 13: Configure Apache Web Server on AWS Linux EC2

> **⏱ Duration:** 30 Minutes
> **📈 Difficulty:** Beginner

---

## 📖 Overview

This lab demonstrates how to deploy an Apache web server on an Amazon Linux EC2 instance. You will launch an EC2 instance, configure HTTP access, install Apache (`httpd`), create a web page, test the deployment, and optionally configure a virtual host.

---

## 🎯 Lab Objectives

By the end of this lab, you will be able to:

* Launch an Amazon Linux EC2 instance.
* Configure security group access for HTTP traffic.
* Install and manage the Apache web server.
* Create and host a basic HTML web page.
* Test web server connectivity.
* Configure an Apache virtual host.

---

## 📋 Prerequisites

Before you begin, ensure you have:

* An active AWS account.
* Access to the AWS Management Console.
* An existing EC2 key pair:

  * `linux-key`
* Basic Linux command-line knowledge.

> ⚠️ **Important**
>
> Allowing HTTP access from anywhere (`0.0.0.0/0`) is suitable for learning labs. For production workloads, restrict access based on application requirements.

---

# Step 1 – Launch EC2 with HTTP Access

## 🖥️ Create the EC2 Instance

Navigate to:

**EC2 → Launch Instance**

Configure the instance:

| Setting        | Value                                    |
| -------------- | ---------------------------------------- |
| Name           | `apache-web-server`                      |
| AMI            | Amazon Linux 2023                        |
| Key Pair       | `linux-key`                              |
| Security Group | Allow SSH (22) + HTTP (80) from Anywhere |

After completing the configuration:

* Click **Launch**.

---

# Step 2 – Install Apache (`httpd`)

## 🔐 Connect to the EC2 Instance

Connect using SSH:

```bash id="u5k9rm"
ssh -i linux-key.pem ec2-user@<PUBLIC_IP>
```

---

## 📦 Install Apache Web Server

Update the system packages:

```bash id="z8v3qx"
sudo yum update -y
```

Install Apache:

```bash id="p6k2wt"
sudo yum install httpd -y
```

Start the Apache service:

```bash id="b7m4fd"
sudo systemctl start httpd
```

Enable Apache to start automatically after reboot:

```bash id="r9x2vh"
sudo systemctl enable httpd
```

Check Apache service status:

```bash id="c4n8ys"
sudo systemctl status httpd
```

> ✅ **Success**
>
> The Apache web server is now installed and running on your EC2 instance.

---

# Step 3 – Create a Web Page

## 🌐 Deploy Website Content

Create an HTML page in the Apache document root:

```bash id="n3q7kw"
sudo sh -c 'echo "<h1>Apache Web Server on AWS EC2!</h1><p>Deployed by Vinod Muleva</p>" > /var/www/html/index.html'
```

The default Apache website directory is:

```text id="q8z5lx"
/var/www/html
```

---

# Step 4 – Test the Web Server

## ✅ Verify Website Access

Open a browser and navigate to:

```text id="v3h7mp"
http://<EC2_PUBLIC_IP>
```

Expected result:

* The Apache web page is displayed successfully.

---

## Test from EC2 Instance

Run:

```bash id="m6w2pt"
curl http://localhost
```

Expected output:

* The HTML content of your Apache page.

---

# Step 5 – Configure Apache Virtual Host (Optional)

## 🌎 Create a Virtual Host Configuration

Create a new Apache configuration file:

```bash id="x7p4qn"
sudo nano /etc/httpd/conf.d/mysite.conf
```

Add the following configuration:

```apache id="d8k5zr"
<VirtualHost *:80>
    ServerName mysite.com
    DocumentRoot /var/www/mysite
    ErrorLog /var/log/httpd/mysite-error.log
</VirtualHost>
```

---

## 📁 Create Website Directory

Create the virtual host document directory:

```bash id="w4n9cs"
sudo mkdir /var/www/mysite
```

Restart Apache:

```bash id="h2m6vx"
sudo systemctl restart httpd
```

> 💡 **Tip**
>
> Virtual hosts allow a single Apache server to host multiple websites or applications using different domain names.

---

## 💡 Best Practices

* Keep Apache packages updated regularly.
* Use HTTPS with SSL/TLS certificates for production websites.
* Restrict security group rules whenever possible.
* Monitor Apache logs for troubleshooting:

  * Access logs
  * Error logs
* Use Elastic Load Balancing for highly available web applications.

---

## ✅ Lab Summary

In this lab, you completed the following tasks:

* Launched an Amazon Linux 2023 EC2 instance.
* Configured HTTP access using a security group.
* Installed Apache (`httpd`).
* Created and hosted a custom web page.
* Tested website access through a public IP address.
* Configured an Apache virtual host.

> 🎯 **Outcome**
>
> You have successfully deployed an Apache web server on AWS EC2. This provides the foundation for hosting websites, APIs, and application workloads on cloud infrastructure.
