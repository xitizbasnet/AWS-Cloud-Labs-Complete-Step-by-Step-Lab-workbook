# 1.8 AWS Serverless & Analytics Services

# Lab 20: Event-Driven Architecture: Lambda + SNS + SQS

> **Module Level:** 🔴 Advanced
> **Estimated Duration:** ⏱️ 60 Minutes

---

# Overview

In this lab, you will build an **event-driven serverless application** using **AWS Lambda**, **Amazon SNS**, **Amazon SQS**, and **Amazon API Gateway**.

The solution processes order requests through a Lambda function, publishes notifications to an SNS topic, delivers messages to an SQS queue, and exposes the application through an HTTP API.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an Amazon SQS queue
* ✅ Create an Amazon SNS topic
* ✅ Subscribe an SQS queue to an SNS topic
* ✅ Develop a Python AWS Lambda function
* ✅ Configure IAM permissions for Lambda
* ✅ Test Lambda with custom events
* ✅ Create an API Gateway trigger
* ✅ Invoke Lambda using an HTTP endpoint

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* IAM permissions to access:

  * AWS Lambda
  * Amazon SNS
  * Amazon SQS
  * Amazon API Gateway
  * AWS IAM
* Basic knowledge of Python
* AWS CLI or `curl` installed (for API testing)

---

# Architecture Overview

```text
                    Client
                      │
          HTTP POST Request
                      │
                      ▼
              Amazon API Gateway
                      │
                      ▼
               AWS Lambda Function
             (process-order)
                      │
          Publishes Notification
                      │
                      ▼
                Amazon SNS Topic
                      │
          Subscription (Amazon SQS)
                      │
                      ▼
               Amazon SQS Queue
```

---

# Step 1 – Create SQS Queue

📍 **Navigation**

```text
SQS → Create queue
```

Configure the following settings:

| Setting       | Value                 |
| ------------- | --------------------- |
| Queue Type    | Standard              |
| Queue Name    | `devops-orders-queue` |
| Configuration | Default settings      |

After completing the configuration:

* Click **Create queue**

Record the following information for later use:

* Queue URL
* Queue ARN

> **Note**
>
> The Queue ARN is required when subscribing the queue to the SNS topic.

---

# Step 2 – Create SNS Topic

📍 **Navigation**

```text
SNS → Topics → Create topic
```

Configure the following settings:

| Setting    | Value                        |
| ---------- | ---------------------------- |
| Topic Type | Standard                     |
| Topic Name | `devops-order-notifications` |

After completing the configuration:

* Click **Create topic**

## Subscribe Amazon SQS to Amazon SNS

📍 **Navigation**

```text
SNS → Subscriptions → Create subscription
```

Configure the subscription:

| Setting  | Value      |
| -------- | ---------- |
| Protocol | Amazon SQS |
| Endpoint | SQS ARN    |

After completing the configuration:

* Click **Create subscription**

> **Success**
>
> Messages published to the SNS topic will now be delivered to the subscribed SQS queue.

---

# Step 3 – Create Lambda Function

📍 **Navigation**

```text
Lambda → Create function → Author from scratch
```

Configure the following settings:

| Setting        | Value                                         |
| -------------- | --------------------------------------------- |
| Function Name  | `process-order`                               |
| Runtime        | Python 3.12                                   |
| Execution Role | Create new role with basic Lambda permissions |

After completing the configuration:

* Click **Create function**

---

# Step 4 – Lambda Code — Process Orders

📍 **Navigation**

```text
Lambda → Function → Code → index.py
```

Replace the default code with the following:

```python
import json
import boto3

sns = boto3.client('sns', region_name='ap-south-1')

SNS_ARN = 'arn:aws:sns:ap-south-1:ACCOUNT_ID:devops-order-notifications'

def lambda_handler(event, context):
    order_id = event.get('order_id', 'ORD-999')
    item = event.get('item', 'Unknown')

    message = (
        f'New Order Received!\n'
        f'Order ID: {order_id}\n'
        f'Item: {item}'
    )

    sns.publish(
        TopicArn=SNS_ARN,
        Message=message,
        Subject='New Order Alert'
    )

    return {
        'statusCode': 200,
        'body': f'Order {order_id} processed!'
    }
```

After updating the code:

* Click **Deploy**

> **Important**
>
> Replace `ACCOUNT_ID` in the `SNS_ARN` value with your AWS account ID before deploying the function.

---

# Step 5 – Add SNS Permission to Lambda

📍 **Navigation**

```text
Lambda → Configuration → Permissions
```

Perform the following steps:

1. Click the Lambda **Execution Role**.
2. AWS IAM opens automatically.
3. Select **Add permissions**.
4. Attach the policy:

```text
AmazonSNSFullAccess
```

> **Note**
>
> For production environments, follow the principle of least privilege by creating a custom IAM policy instead of using `AmazonSNSFullAccess`.

---

# Step 6 – Test Lambda

📍 **Navigation**

```text
Lambda → Test → Create new test event
```

Configure the following:

| Setting    | Value        |
| ---------- | ------------ |
| Event Name | `test-order` |

Proceed to the next step to add the test event JSON.

---

# Step 7 – Test Event JSON

Use the following JSON payload:

```json
{
  "order_id": "ORD-2024-001",
  "item": "AWS Certified Solutions Architect Book",
  "quantity": 1,
  "customer": "Vinod Muleva"
}
```

Click:

* **Test**

Expected results:

* Lambda execution completes successfully.
* A success response is displayed.

Verify message delivery:

📍 **Amazon SQS**

```text
SQS → Send and receive messages → Poll for messages
```

If an email subscription exists for the SNS topic:

* Check your email inbox for the notification.

> **Success**
>
> The Lambda function publishes a notification to Amazon SNS, which forwards the message to the subscribed Amazon SQS queue.

---

# Step 8 – Create API Gateway Trigger

📍 **Navigation**

```text
Lambda → Add trigger → API Gateway
```

Configure the following settings:

| Setting  | Value          |
| -------- | -------------- |
| API Type | HTTP API       |
| Action   | Create new API |
| Security | Open           |

After completing the configuration:

* Click **Add**

Copy the generated API URL.

Test the API using `curl`.

```bash
curl -X POST <API_URL> \
-H "Content-Type: application/json" \
-d '{"order_id":"ORD-002","item":"Docker Book"}'
```

> **Success**
>
> The HTTP request invokes the Lambda function, which processes the order and publishes a notification to the configured SNS topic.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ Amazon SQS queue created successfully
* ✅ Amazon SNS topic created
* ✅ SQS subscribed to the SNS topic
* ✅ Lambda function deployed successfully
* ✅ Lambda execution role configured with SNS permissions
* ✅ Test event executed successfully
* ✅ Notification received in the SQS queue
* ✅ API Gateway trigger created
* ✅ HTTP request successfully invokes the Lambda function

---

# Best Practices

* 💡 Follow the **principle of least privilege** by assigning only the permissions required by the Lambda execution role.
* 💡 Store configuration values such as SNS topic ARNs in **Lambda environment variables** instead of hardcoding them.
* 💡 Enable Amazon CloudWatch Logs to monitor Lambda executions and troubleshoot issues.
* 💡 Use Amazon SQS to decouple application components and improve reliability.
* 💡 Configure Dead Letter Queues (DLQs) for failed message processing in production environments.
* 💡 Protect API Gateway endpoints using authentication and authorization mechanisms such as IAM, Amazon Cognito, or Lambda Authorizers.

---

# Troubleshooting

| Issue                               | Possible Cause                         | Resolution                                                                                               |
| ----------------------------------- | -------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Lambda fails to publish to SNS      | Missing IAM permissions                | Verify that the Lambda execution role has permission to publish to the SNS topic.                        |
| No messages appear in the SQS queue | Subscription not configured or pending | Confirm that the Amazon SQS subscription is active and attached to the correct SNS topic.                |
| Lambda returns an error             | Incorrect SNS ARN                      | Verify that the `SNS_ARN` contains the correct AWS account ID and topic name.                            |
| API Gateway returns an error        | Trigger not configured correctly       | Confirm that the API Gateway trigger is attached to the Lambda function and that the API URL is correct. |
| `curl` request fails                | Invalid request format or endpoint     | Verify the API URL, HTTP method (`POST`), and JSON payload syntax.                                       |
