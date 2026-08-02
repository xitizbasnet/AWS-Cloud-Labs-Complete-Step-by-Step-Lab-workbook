# 1.6 AWS Monitoring Services

# LAB 17: CloudWatch — CPU Alarm & EC2 Monitoring

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will configure **Amazon CloudWatch** to monitor Amazon EC2 CPU utilization, create a CloudWatch alarm, receive email notifications through **Amazon SNS**, and enable **AWS CloudTrail** to record all AWS API activity.

By the end of this lab, you will have proactive monitoring and auditing configured for your EC2 environment.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Enable detailed monitoring for an EC2 instance
* ✅ Create a CloudWatch CPU utilization alarm
* ✅ Configure Amazon SNS email notifications
* ✅ Generate CPU load to trigger an alarm
* ✅ Monitor alarm state transitions
* ✅ Enable AWS CloudTrail for API auditing

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Running Amazon EC2 instance
* EC2 Key Pair (`linux-key.pem`)
* Valid email address for SNS notifications
* Permissions to access:

  * Amazon EC2
  * Amazon CloudWatch
  * Amazon SNS
  * AWS CloudTrail
  * Amazon S3

---

# Architecture Overview

```text
                   EC2 Instance
                        │
                 CPU Utilization
                        │
                        ▼
                Amazon CloudWatch
                        │
                CPU Alarm (>70%)
                        │
                        ▼
                  Amazon SNS Topic
                        │
                        ▼
                Email Notification

────────────────────────────────────────────

AWS API Activity
        │
        ▼
 AWS CloudTrail
        │
        ▼
    Amazon S3 Bucket
```

---

# Step 1 – Enable Detailed Monitoring on EC2

📍 **Navigation**

```text
EC2 → Select Instance → Actions → Monitor and troubleshoot → Enable detailed monitoring
```

Perform the following steps:

1. Select the EC2 instance.
2. Click **Actions**.
3. Select **Monitor and troubleshoot**.
4. Choose **Enable detailed monitoring**.
5. Click **Yes, Enable**.

> **Note**
>
> * **Standard Monitoring** collects metrics every **5 minutes**.
> * **Detailed Monitoring** collects metrics every **1 minute**, providing faster visibility into instance performance.

---

# Step 2 – Create CloudWatch CPU Alarm

📍 **Navigation**

```text
CloudWatch → Alarms → All Alarms → Create Alarm
```

Configure the alarm:

### Select Metric

1. Click **Select metric**.
2. Navigate to:

   * **EC2**
   * **Per-Instance Metrics**
3. Search for your EC2 **Instance ID**.
4. Select the **CPUUtilization** metric.

### Configure Conditions

| Setting    | Value             |
| ---------- | ----------------- |
| Threshold  | Greater than `70` |
| Datapoints | `2 out of 3`      |

After configuring the conditions:

* Click **Next**

---

# Step 3 – Add SNS Notification

Configure Amazon SNS for email notifications.

Create a new SNS topic:

| Setting    | Value               |
| ---------- | ------------------- |
| SNS Topic  | `ec2-cpu-alerts`    |
| Email      | `your@email.com`    |
| Alarm Name | `HighCPU-WebServer` |

After completing the configuration:

1. Click **Create topic**.
2. Click **Create alarm**.
3. Open your email inbox.
4. Confirm the SNS subscription.

> **Important**
>
> The CloudWatch alarm will not send notifications until the SNS email subscription has been confirmed.

---

# Step 4 – Test the Alarm

Connect to the EC2 instance using SSH.

```bash
ssh -i linux-key.pem ec2-user@<EC2_IP>
```

Install the **stress** utility.

```bash
sudo yum install stress -y
```

Generate CPU load.

```bash
stress --cpu 4 --timeout 300 &
```

Monitor the CloudWatch alarm.

Expected alarm state transitions:

```text
OK
   ↓
INSUFFICIENT_DATA
   ↓
ALARM
```

Expected results:

* CPU utilization exceeds **70%**.
* Alarm state changes to **ALARM**.
* Email notification is received within approximately **5 minutes**.

Stop the CPU stress test.

```bash
kill %1
```

> **Success**
>
> The alarm transitions to the **ALARM** state and an email notification is delivered through Amazon SNS.

---

# Step 5 – Enable CloudTrail

📍 **Navigation**

```text
Search CloudTrail → Create trail
```

Configure the trail:

| Setting                | Value                |
| ---------------------- | -------------------- |
| Trail Name             | `devops-audit-trail` |
| S3 Bucket              | Create new           |
| Enable for all Regions | Yes                  |
| Management Events      | All (Read + Write)   |

After completing the configuration:

* Click **Create trail**

> **Success**
>
> All AWS API calls are now recorded and stored in the configured Amazon S3 bucket.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Detailed monitoring enabled on the EC2 instance
* ✅ CloudWatch CPU alarm created successfully
* ✅ SNS topic created
* ✅ Email subscription confirmed
* ✅ Alarm transitions from **OK** to **ALARM**
* ✅ Email notification received
* ✅ CloudTrail enabled
* ✅ CloudTrail logs stored in Amazon S3

---

# Best Practices

* 💡 Enable **Detailed Monitoring** for production workloads that require near real-time metrics.
* 💡 Configure CloudWatch alarms for critical performance metrics such as CPU, memory (via CloudWatch Agent), disk utilization, and network throughput.
* 💡 Use Amazon SNS to notify administrators of operational events.
* 💡 Enable CloudTrail in **all AWS Regions** to maintain comprehensive audit logs.
* 💡 Protect CloudTrail log buckets using encryption and restrictive IAM policies.
* 💡 Periodically review CloudWatch alarms and CloudTrail logs to identify operational issues and security events.

---

# Troubleshooting

| Issue                                  | Possible Cause                                           | Resolution                                                                                               |
| -------------------------------------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Alarm remains in **INSUFFICIENT_DATA** | CloudWatch has not collected enough metric data          | Wait several minutes for additional metrics to be collected.                                             |
| Alarm does not transition to **ALARM** | CPU utilization does not exceed the configured threshold | Verify that the `stress` process is running and monitor the CPUUtilization metric.                       |
| Email notification not received        | SNS subscription not confirmed                           | Confirm the subscription using the email sent by Amazon SNS.                                             |
| CloudTrail logs are not visible        | Trail configuration or delivery delay                    | Verify the trail is enabled and allow several minutes for logs to appear in the S3 bucket.               |
| Unable to install the `stress` utility | Package repository or network connectivity issue         | Ensure the EC2 instance has internet access and that the appropriate package repositories are available. |
