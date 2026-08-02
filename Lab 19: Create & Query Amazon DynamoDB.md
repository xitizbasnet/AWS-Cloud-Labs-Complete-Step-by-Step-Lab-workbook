# 1.7 AWS Database Services

# Lab 19: Create & Query Amazon DynamoDB

> **Module Level:** 🟡 Intermediate
> **Estimated Duration:** ⏱️ 30 Minutes

---

# Overview

In this lab, you will create a **NoSQL database** using **Amazon DynamoDB**, add items to the table, perform queries and scans, and interact with the table using the **AWS CLI**.

By the end of this lab, you will understand how DynamoDB stores data using partition and sort keys and how to manage items through both the AWS Management Console and the AWS CLI.

---

# Learning Objectives

After completing this lab, you will be able to:

* ✅ Create an Amazon DynamoDB table
* ✅ Define partition and sort keys
* ✅ Insert items into a DynamoDB table
* ✅ Query items using the partition key
* ✅ Perform a table scan
* ✅ Manage DynamoDB items using the AWS CLI

---

# Prerequisites

Before starting this lab, ensure you have:

* AWS Account
* AWS CLI installed and configured
* Appropriate IAM permissions to access:

  * Amazon DynamoDB
  * AWS CLI

---

# Architecture Overview

```text
          AWS Management Console
                  │
                  │
                  ▼
          Amazon DynamoDB Table
          DevOpsEmployees
                  │
      ┌───────────┴───────────┐
      │                       │
      ▼                       ▼
  Query Items             Scan Table
      │                       │
      └───────────┬───────────┘
                  ▼
               AWS CLI
```

---

# Step 1 – Create DynamoDB Table

📍 **Navigation**

```text
DynamoDB → Tables → Create table
```

Configure the following settings:

| Setting        | Value                        |
| -------------- | ---------------------------- |
| Table name     | `DevOpsEmployees`            |
| Partition key  | `employeeId` (String)        |
| Sort key       | `department` (String)        |
| Table settings | Default settings (Free tier) |

After completing the configuration:

* Click **Create table**

> **Note**
>
> The combination of the **Partition Key** and **Sort Key** uniquely identifies each item in the table.

---

# Step 2 – Create Items

📍 **Navigation**

```text
Table → Explore table items → Create item
```

Create an item with the following attributes:

| Attribute  | Value                |
| ---------- | -------------------- |
| employeeId | `EMP001`             |
| department | `DevOps`             |
| name       | `Vinod Muleva`       |
| skills     | `[Docker, K8s, AWS]` |

After entering the values:

* Click **Create item**

Add additional items to the table as needed.

> **Tip**
>
> Populate the table with multiple items to better understand query and scan operations.

---

# Step 3 – Query the Table

📍 **Navigation**

```text
Explore table items → Query
```

Configure the query:

| Setting             | Value    |
| ------------------- | -------- |
| Partition key value | `EMP001` |

Click:

* **Run**

Expected result:

* The item associated with **EMP001** is returned.

### Perform a Full Table Scan

📍 **Navigation**

```text
Explore table items → Scan
```

Click:

* **Run**

> **Note**
>
> A **Query** retrieves items based on the partition key, whereas a **Scan** examines every item in the table. Queries are generally more efficient and cost-effective than scans.

---

# Step 4 – Using AWS CLI with DynamoDB

## Put Item

Run the following command to add a new item to the table.

```bash
aws dynamodb put-item \
  --table-name DevOpsEmployees \
  --item '{"employeeId":{"S":"EMP003"},"department":{"S":"CloudOps"},"name":{"S":"Priya K"}}' \
  --region ap-south-1
```

## Get Item

Run the following command to retrieve an existing item.

```bash
aws dynamodb get-item \
  --table-name DevOpsEmployees \
  --key '{"employeeId":{"S":"EMP001"},"department":{"S":"DevOps"}}' \
  --region ap-south-1
```

> **Success**
>
> The AWS CLI returns the requested item in JSON format if it exists in the table.

---

# Validation Checklist

Verify the following before completing the lab:

* ✅ DynamoDB table created successfully
* ✅ Partition key and sort key configured correctly
* ✅ Sample items added to the table
* ✅ Query operation returns the expected item
* ✅ Scan operation lists all table items
* ✅ `put-item` command executes successfully
* ✅ `get-item` command retrieves the correct item

---

# Best Practices

* 💡 Design partition keys carefully to distribute data evenly across partitions.
* 💡 Use **Query** instead of **Scan** whenever possible to improve performance and reduce costs.
* 💡 Enable Point-in-Time Recovery (PITR) for production DynamoDB tables.
* 💡 Use IAM policies to restrict access to DynamoDB resources.
* 💡 Monitor table performance using **Amazon CloudWatch** metrics.
* 💡 Use on-demand or provisioned capacity based on your workload requirements.

---

# Troubleshooting

| Issue                                | Possible Cause                            | Resolution                                                                                             |
| ------------------------------------ | ----------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Unable to create the table           | Insufficient IAM permissions              | Verify that your IAM user or role has permissions to create DynamoDB tables.                           |
| Query returns no results             | Incorrect partition key or sort key value | Confirm that the values exactly match an existing item in the table.                                   |
| `put-item` command fails             | Invalid JSON syntax                       | Verify the JSON structure and ensure quotation marks are properly escaped if required by your shell.   |
| `get-item` returns an empty response | Item does not exist                       | Confirm that both the partition key and sort key values match an existing record.                      |
| AWS CLI command fails                | CLI not configured                        | Verify that the AWS CLI is installed and configured with valid credentials and the correct AWS Region. |
