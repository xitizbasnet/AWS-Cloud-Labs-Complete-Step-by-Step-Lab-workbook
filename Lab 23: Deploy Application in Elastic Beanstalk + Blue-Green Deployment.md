# 1.9 AWS Automation & Configuration Management

# Lab 23: Deploy Application in Elastic Beanstalk + Blue-Green Deployment

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 45 Minutes

---

# Overview

In this lab, you will deploy a web application using **AWS Elastic Beanstalk** and implement a **Blue-Green Deployment strategy**.

You will create a Python Flask application, deploy it to Elastic Beanstalk, create a second environment for the updated application version, test it, and swap environment URLs to perform a production release with minimal downtime.

You will also configure **Immutable Deployment** to improve application deployment reliability.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create and package a Python Flask application
* ✅ Deploy an application using Elastic Beanstalk
* ✅ Configure Elastic Beanstalk environments
* ✅ Perform Blue-Green deployments
* ✅ Swap environment URLs for production releases
* ✅ Roll back easily using the previous environment
* ✅ Configure Immutable Deployment strategy

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Basic knowledge of Python and Flask
* AWS Elastic Beanstalk access
* EC2 Key Pair:

  * `linux-key`
* Required local tools:

  * Python
  * ZIP utility
  * AWS CLI (optional)

---

# Architecture Overview

```text id="x8js3v"
                  Elastic Beanstalk Application
                           │
          ┌────────────────┴────────────────┐
          │                                 │
          ▼                                 ▼

   Blue Environment                 Green Environment
   devops-webapp                    devops-webapp-green
   Version 1.0                      Version 2.0

          │                                 │
          └────────── URL Swap ─────────────┘
                         │
                         ▼
                  Production Traffic
```

---

# Step 1 – Create Sample Application

Create a new application directory:

```bash id="k9n8dj"
mkdir beanstalk-app && cd beanstalk-app
```

---

## Create Application File

Create:

```text id="3c9q0n"
application.py
```

Add the following code:

```python id="2n2m9q"
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return '<h1>Elastic Beanstalk v1.0</h1>'

if __name__ == '__main__':
    app.run()
```

---

## Create Requirements File

Create:

```text id="2x8l2d"
requirements.txt
```

Add:

```text id="2c8m4q"
flask==2.3.0
gunicorn==21.2.0
```

---

## Create Procfile

Create:

```text id="r0f1xm"
Procfile
```

Add:

```text id="v4q7ds"
web: gunicorn application:app
```

> **Note**
>
> The `Procfile` tells Elastic Beanstalk how to start the application.

---

## Package Application

Create the deployment package:

```bash id="8h1q3m"
zip -r app-v1.zip .
```

---

# Step 2 – Create Elastic Beanstalk Application

📍 **Navigation**

```text id="3g8k6w"
Elastic Beanstalk → Create application
```

Configure the application:

| Setting          | Value                       |
| ---------------- | --------------------------- |
| Application Name | `devops-webapp`             |
| Platform         | Python 3.11                 |
| Application Code | Upload your code            |
| Upload File      | `app-v1.zip`                |
| Preset           | Single instance (Free tier) |
| Service Role     | Create new                  |
| EC2 Key Pair     | `linux-key`                 |

After completing the configuration:

* Click **Create application**

> **Note**
>
> Environment creation may take approximately **5–10 minutes**.

---

# Step 3 – Test Elastic Beanstalk Application

📍 **Navigation**

```text id="w7g6m2"
Elastic Beanstalk → Environment
```

Perform the following steps:

1. Click the environment URL.
2. Verify the application output.

Expected result:

```html
Elastic Beanstalk v1.0
```

> **Success**
>
> The Flask application is successfully deployed and running on Elastic Beanstalk.

---

# Step 4 – Blue-Green Deployment

Blue-Green Deployment creates a separate environment for the new application version before switching production traffic.

---

## Create Version 2 Application

Modify the application response:

Change:

```python
return '<h1>Elastic Beanstalk v1.0</h1>'
```

To:

```python
return '<h1>Elastic Beanstalk v2.0 - Blue-Green!</h1>'
```

---

Package the updated version:

```bash id="z7c2pq"
zip -r app-v2.zip .
```

---

## Clone Existing Environment

📍 **Navigation**

```text id="7f9x1h"
Elastic Beanstalk → Environment → Actions → Clone environment
```

Configure:

| Setting              | Value                 |
| -------------------- | --------------------- |
| New Environment Name | `devops-webapp-green` |

Perform the following:

1. Click **Clone**.
2. Deploy `app-v2.zip` to the green environment.
3. Test the green environment URL.

Expected result:

```html
Elastic Beanstalk v2.0 - Blue-Green!
```

---

# Step 5 – Swap URLs (Go Live with v2)

After validating the green environment:

📍 **Navigation**

```text id="n6x4j0"
Elastic Beanstalk → Environment → Actions → Swap environment URLs
```

Configure:

| Setting             | Value                 |
| ------------------- | --------------------- |
| Environment to swap | `devops-webapp-green` |

Click:

* **Swap**

Expected result:

* Original production URL now serves **v2**
* Previous environment URL continues serving **v1**

> **Success**
>
> Blue-Green deployment is completed. The previous version remains available for quick rollback.

---

# Step 6 – Immutable Deployment

Immutable deployment creates new EC2 instances for the updated version and replaces them only after successful health checks.

📍 **Navigation**

```text id="7j3h9p"
Elastic Beanstalk → Configuration → Updates → Edit
```

Configure:

| Setting           | Value     |
| ----------------- | --------- |
| Deployment Policy | Immutable |

Apply the configuration:

1. Click **Apply**.
2. Upload a new application version.

Expected behavior:

* AWS creates new EC2 instances.
* New instances are tested for health.
* Traffic switches only after successful deployment.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Flask application created successfully
* ✅ Application packaged as ZIP file
* ✅ Elastic Beanstalk environment created
* ✅ Application deployed successfully
* ✅ Version 1.0 tested
* ✅ Green environment created
* ✅ Version 2.0 deployed to green environment
* ✅ Environment URLs swapped successfully
* ✅ Immutable deployment configured

---

# Best Practices

* 💡 Use Blue-Green deployment for production releases requiring minimal downtime.
* 💡 Always test the new version in a separate environment before switching traffic.
* 💡 Keep the previous environment available for rollback.
* 💡 Use Immutable Deployment for critical applications where failed deployments must not affect production.
* 💡 Store application versions in source control and maintain release history.
* 💡 Use environment variables instead of hardcoding application configuration values.

---

# Troubleshooting

| Issue                              | Possible Cause                    | Resolution                                                                                     |
| ---------------------------------- | --------------------------------- | ---------------------------------------------------------------------------------------------- |
| Elastic Beanstalk deployment fails | Incorrect application structure   | Verify `application.py`, `requirements.txt`, and `Procfile` are included in the ZIP file root. |
| Application URL shows an error     | Flask application failed to start | Check Elastic Beanstalk logs and verify the Gunicorn configuration.                            |
| Green environment deployment fails | Application version issue         | Review environment events and application logs.                                                |
| URL swap fails                     | Environment health check failure  | Ensure both environments show a healthy status before swapping URLs.                           |
| Rollback required after deployment | New version has issues            | Swap URLs back to the previous environment version.                                            |
