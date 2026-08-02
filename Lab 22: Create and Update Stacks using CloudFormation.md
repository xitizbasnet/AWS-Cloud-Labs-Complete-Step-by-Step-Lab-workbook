# 1.9 AWS Automation & Configuration Management

# Lab 22: Create and Update Stacks using CloudFormation

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 45 Minutes

---

# Overview

In this lab, you will use **AWS CloudFormation** to deploy and manage AWS infrastructure using **Infrastructure as Code (IaC)**.

You will create a CloudFormation YAML template that provisions an EC2 instance, security group, and web server configuration automatically. You will then deploy, update, and delete the CloudFormation stack.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Understand AWS CloudFormation concepts
* ✅ Create infrastructure templates using YAML
* ✅ Deploy AWS resources through CloudFormation stacks
* ✅ View stack outputs
* ✅ Update existing CloudFormation stacks
* ✅ Delete stacks and automatically remove resources

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* EC2 Key Pair:

  * `linux-key`
* IAM permissions to access:

  * AWS CloudFormation
  * Amazon EC2
  * Amazon VPC
* Basic understanding of YAML syntax

---

# Architecture Overview

```text id="t8s0w2"
              CloudFormation Template
                    (YAML)
                       │
                       ▼
              AWS CloudFormation Stack
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ▼                             ▼
  EC2 Instance                 Security Group
        │
        ▼
 Apache Web Server
        │
        ▼
 "CloudFormation Stack!"
 Web Page
```

---

# Step 1 – Understand CloudFormation

## What is AWS CloudFormation?

**AWS CloudFormation** is an Infrastructure as Code (IaC) service that allows you to define and provision AWS resources using YAML or JSON templates.

### Key Benefits

* 🔁 **Repeatable** — Deploy the same infrastructure consistently.
* 📌 **Version Controlled** — Store templates in source control systems.
* ⚙️ **Automated** — Create and manage resources without manual configuration.
* 📦 **Resource Management** — Manage related AWS resources as a single stack.

---

# Step 2 – Create CloudFormation Template

Create a new CloudFormation template file:

```text id="2f4v0n"
ec2-stack.yaml
```

This template will create:

* EC2 instance
* Security Group
* Apache web server configuration
* Stack output containing the EC2 public IP address

---

# Step 3 – EC2 Stack YAML Template

Add the following content to `ec2-stack.yaml`:

```yaml id="s4r2a1"
AWSTemplateFormatVersion: '2010-09-09'

Description: 'DevOps Lab EC2 Stack'

Parameters:

  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
    Description: EC2 instance type

  KeyName:
    Type: String
    Default: linux-key

Resources:

  DevOpsSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: DevOps Lab SG
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  DevOpsEC2:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: ami-0f58b397bc5c1f2e8
      KeyName: !Ref KeyName
      SecurityGroupIds:
        - !Ref DevOpsSecurityGroup

      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum update -y
          yum install httpd -y
          systemctl start httpd
          echo "<h1>CloudFormation Stack!</h1>" > /var/www/html/index.html

      Tags:
        - Key: Name
          Value: CFN-EC2

Outputs:

  PublicIP:
    Value: !GetAtt DevOpsEC2.PublicIp
    Description: EC2 Public IP
```

> **Note**
>
> The CloudFormation template defines the desired infrastructure state. AWS automatically creates the required resources and manages dependencies between them.

---

# Step 4 – Deploy the Stack

📍 **Navigation**

```text id="c8d9sl"
CloudFormation → Stacks → Create stack
```

Configure the stack:

### Template Source

Select:

```text id="1d5wzi"
Upload template file
```

Upload:

```text id="2v3ql0"
ec2-stack.yaml
```

### Stack Configuration

| Setting    | Value                 |
| ---------- | --------------------- |
| Stack Name | `devops-ec2-stack`    |
| Parameters | Review defaults       |
| Tag        | `Project = DevOpsLab` |

After completing the configuration:

1. Click **Next**.
2. Review settings.
3. Click **Create stack**.

Monitor the stack:

```text id="m1f4i4"
CloudFormation → Events
```

Expected status:

```text id="7f0xjd"
CREATE_COMPLETE
```

> **Success**
>
> CloudFormation has successfully created the EC2 instance and associated security group.

---

# Step 5 – Test the Stack Output

📍 **Navigation**

```text id="w5s8h8"
CloudFormation → Stack → Outputs
```

Perform the following steps:

1. Copy the `PublicIP` output value.
2. Open a browser.
3. Navigate to:

```text
http://<IP>
```

Expected result:

```text
CloudFormation Stack!
```

> **Success**
>
> The Apache web server installed through User Data is serving the CloudFormation test page.

---

# Step 6 – Update the Stack

Modify the `UserData` section in the YAML template to add additional content.

Example:

```bash
echo "<h1>CloudFormation Stack Updated!</h1>" > /var/www/html/index.html
```

Update the existing stack.

📍 **Navigation**

```text id="3i7x9p"
CloudFormation → Stack → Update
```

Select:

```text id="0q4qpp"
Replace current template → Upload updated template
```

After uploading:

1. Click **Update stack**.
2. Monitor the Events tab.

Expected status:

```text id="g4x9py"
UPDATE_COMPLETE
```

---

# Step 7 – Delete the Stack

📍 **Navigation**

```text id="z9z7tr"
CloudFormation → Stack → Delete
```

Confirm the deletion.

CloudFormation automatically removes:

* EC2 Instance
* Security Group
* Associated stack resources

> **Important**
>
> Deleting a CloudFormation stack removes all resources managed by that stack unless deletion policies are configured.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ CloudFormation template created successfully
* ✅ EC2 stack deployed
* ✅ Security Group created automatically
* ✅ Apache installed using User Data
* ✅ Stack output displayed the EC2 public IP
* ✅ Web page accessible through browser
* ✅ Stack updated successfully
* ✅ Stack deleted successfully

---

# Best Practices

* 💡 Store CloudFormation templates in version control systems such as Git.
* 💡 Use parameters to make templates reusable across environments.
* 💡 Avoid hardcoding values such as AMI IDs, account IDs, and credentials.
* 💡 Use AWS CloudFormation change sets before applying production updates.
* 💡 Apply stack tags for resource tracking and cost management.
* 💡 Use nested stacks for large and complex infrastructure deployments.

---

# Troubleshooting

| Issue                           | Possible Cause                                | Resolution                                                                        |
| ------------------------------- | --------------------------------------------- | --------------------------------------------------------------------------------- |
| Stack creation fails            | Invalid YAML syntax                           | Validate the template before deployment using CloudFormation template validation. |
| EC2 instance does not launch    | Incorrect AMI ID or unavailable instance type | Verify the AMI ID exists in the selected AWS Region.                              |
| Web page does not load          | Security Group missing HTTP access            | Confirm inbound rule allows TCP port 80.                                          |
| Stack update fails              | Invalid template modification                 | Review CloudFormation Events for the specific failure reason.                     |
| Resources remain after deletion | Resources created outside the stack           | Verify that all resources are managed by the CloudFormation stack.                |
