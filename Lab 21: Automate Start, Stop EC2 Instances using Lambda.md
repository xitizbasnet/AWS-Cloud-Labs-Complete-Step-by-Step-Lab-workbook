# 1.8 AWS Serverless & Analytics Services

# Lab 21: Automate Start/Stop EC2 Instances using Lambda

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will automate Amazon EC2 instance management using **AWS Lambda** and **Amazon EventBridge Scheduler**.

You will create a Lambda function that identifies EC2 instances based on a specific tag and automatically stops running instances on a scheduled basis.

This automation helps reduce unnecessary compute costs by shutting down non-production or development environments outside working hours.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an AWS Lambda function for EC2 automation
* ✅ Use the AWS SDK (`boto3`) to manage EC2 resources
* ✅ Configure IAM permissions for Lambda
* ✅ Tag EC2 instances for automated actions
* ✅ Create an EventBridge scheduled rule
* ✅ Automate EC2 instance shutdown operations

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* Running EC2 instance
* Permissions to access:

  * AWS Lambda
  * Amazon EC2
  * AWS IAM
  * Amazon EventBridge
* Basic knowledge of Python

---

# Architecture Overview

```text id="s2xgqf"
                 Amazon EventBridge
                       │
             Scheduled Rule (Cron)
                       │
                       ▼
              AWS Lambda Function
          (stop-ec2-instances)
                       │
                       ▼
              Amazon EC2 Service
                       │
          Finds Tagged Instances
          AutoStop=true
                       │
                       ▼
              Stop EC2 Instances
```

---

# Step 1 – Create Lambda Function — Stop EC2

📍 **Navigation**

```text id="c1lqk9"
Lambda → Create function
```

Configure the following settings:

| Setting       | Value                |
| ------------- | -------------------- |
| Function Name | `stop-ec2-instances` |
| Runtime       | Python 3.12          |

After creating the function, update the code.

---

# Step 2 – Stop EC2 Lambda Code

📍 **Navigation**

```text id="0v0qk1"
Lambda → Function → Code
```

Replace the default code with the following:

```python id="y0c4wj"
import boto3

ec2 = boto3.client('ec2', region_name='ap-south-1')

# Tag your EC2 with:
# Key=AutoStop
# Value=true

def lambda_handler(event, context):
    response = ec2.describe_instances(
        Filters=[
            {
                'Name': 'tag:AutoStop',
                'Values': ['true']
            },
            {
                'Name': 'instance-state-name',
                'Values': ['running']
            }
        ]
    )

    instance_ids = []

    for r in response['Reservations']:
        for i in r['Instances']:
            instance_ids.append(i['InstanceId'])

    if instance_ids:
        ec2.stop_instances(
            InstanceIds=instance_ids
        )

        return f'Stopped: {instance_ids}'

    return 'No instances to stop'
```

After updating the code:

* Click **Deploy**

---

# Step 3 – Add EC2 Permission to Lambda Role

📍 **Navigation**

```text id="9f5j7v"
IAM → Roles → Find Lambda execution role
```

Attach the following policy:

```text id="3u4qpp"
AmazonEC2FullAccess
```

> **Important**
>
> For production environments, replace broad permissions with a custom IAM policy that only allows required EC2 actions such as describing and stopping instances.

---

# Step 4 – Tag EC2 Instances

📍 **Navigation**

```text id="apj2v5"
EC2 → Select Instance → Tags → Manage tags
```

Add the following tag:

| Key        | Value  |
| ---------- | ------ |
| `AutoStop` | `true` |

Example:

```text id="b0vl5p"
AutoStop=true
```

> **Note**
>
> Only EC2 instances with this tag will be identified and stopped by the Lambda function.

---

# Step 5 – Create EventBridge Schedule

📍 **Navigation**

```text id="xj1m0t"
EventBridge → Rules → Create rule
```

Configure the rule:

| Setting         | Value                |
| --------------- | -------------------- |
| Rule Name       | `stop-ec2-nightly`   |
| Rule Type       | Schedule             |
| Cron Expression | `0 18 * * ? *`       |
| Target          | Lambda               |
| Lambda Function | `stop-ec2-instances` |

Create the rule.

> **Note**
>
> The cron expression:
>
> ```text
> 0 18 * * ? *
> ```
>
> runs every day at **18:00 UTC**.
>
> For this lab:
>
> **6 PM IST = 12:30 UTC**
>
> Adjust the cron expression according to your required timezone.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Lambda function created successfully
* ✅ EC2 automation code deployed
* ✅ Lambda execution role has EC2 permissions
* ✅ EC2 instance tagged with `AutoStop=true`
* ✅ EventBridge scheduled rule created
* ✅ Lambda function configured as the target
* ✅ Scheduled execution stops tagged EC2 instances

---

# Best Practices

* 💡 Use resource tags to control automation scope safely.
* 💡 Avoid using `AmazonEC2FullAccess` in production; create a least-privilege IAM policy.
* 💡 Add CloudWatch Logs permissions to monitor Lambda execution results.
* 💡 Use separate tags for different automation workflows, such as:

  * `AutoStart=true`
  * `AutoStop=true`
  * `Environment=Dev`
* 💡 Test Lambda manually before enabling automated schedules.
* 💡 Use AWS Systems Manager Automation for complex operational workflows.

---

# Troubleshooting

| Issue                                    | Possible Cause                                       | Resolution                                                           |
| ---------------------------------------- | ---------------------------------------------------- | -------------------------------------------------------------------- |
| Lambda does not stop instances           | EC2 instance missing the required tag                | Verify the instance has `AutoStop=true` tag.                         |
| Lambda execution fails                   | Missing EC2 permissions                              | Confirm the Lambda execution role has required EC2 permissions.      |
| No instances are found                   | Instance is already stopped or filter does not match | Verify the instance state is `running` and the tag value is correct. |
| EventBridge rule does not trigger Lambda | Incorrect target configuration                       | Confirm the Lambda function is selected as the EventBridge target.   |
| Schedule runs at the wrong time          | UTC timezone difference                              | Convert your local time to UTC before creating the cron expression.  |
