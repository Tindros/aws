---
title : "Create IAM Lambda Execution Role"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.5.1 </b> "
---

AWS Lambda functions assume an **IAM Execution Role** at runtime to determine what AWS resources and APIs the function code is authorized to access. Following the AWS Well-Architected Security Pillar, we enforce the **Principle of Least Privilege**: granting only the exact permissions needed for DynamoDB operations and CloudWatch logging, scoped to our specific table ARN.

---

#### Step 1: Start IAM Role Creation

1. Open the [AWS IAM Console](https://console.aws.amazon.com/iam/home#/roles).
2. In the navigation pane, choose **Roles**, then click **Create role**.
3. Under **Trusted entity type**, select **AWS service**.
4. Under **Use case**, select **Lambda** from the common use cases list.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-01-iam-role-create.png`  
*(To be captured during deployment: IAM Create Role screen selecting Lambda as the trusted entity)*
{{% /notice %}}
<!-- ![Create IAM Role](/images/5-Workshop/5.5-Lambda/lambda-01-iam-role-create.png) -->

5. Click **Next**.

---

#### Step 2: Attach Permissions Policies

1. In the **Add permissions** filter box, search for `AWSLambdaBasicExecutionRole`.
2. Check the box next to **AWSLambdaBasicExecutionRole**:
   + This AWS-managed policy grants permission to upload logs to **Amazon CloudWatch Logs** (`logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`).
3. Click **Next**.

---

#### Step 3: Name and Review the Role

1. **Role name:** Enter `ExpenseTrackerLambdaRole`.
2. **Description:** Enter `Execution role for Expense Tracker Lambda with DynamoDB access and CloudWatch logging`.
3. Verify that the **Trust policy** allows the `lambda.amazonaws.com` service principal:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "lambda.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```
4. Click **Create role**.

---

#### Step 4: Add Least-Privilege DynamoDB Inline Policy

1. From the Roles list, click on the newly created role: `ExpenseTrackerLambdaRole`.
2. Under the **Permissions** tab, click **Add permissions** and choose **Create inline policy**.
3. In the Policy editor, switch to the **JSON** tab and replace the default content with the following policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ExpenseTableScopedAccess",
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query"
      ],
      "Resource": "arn:aws:dynamodb:us-east-1:*:table/ExpenseTable"
    }
  ]
}
```

{{% notice tip %}}
**Security Audit:**
+ The policy limits actions to standard single-item CRUD operations and partitioned `Query`. Dangerous operations like `dynamodb:DeleteTable` or unrestricted full-database `dynamodb:Scan` are blocked.
+ The `Resource` ARN restricts access specifically to `ExpenseTable` in `us-east-1`, preventing cross-table access.
{{% /notice %}}

4. Click **Next**.
5. **Policy name:** Enter `ExpenseTrackerDynamoDBPolicy`.
6. Click **Create policy**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.5-Lambda/lambda-02-iam-policies.png`  
*(To be captured during deployment: ExpenseTrackerLambdaRole permissions tab showing attached policies)*
{{% /notice %}}
<!-- ![Attached IAM Policies](/images/5-Workshop/5.5-Lambda/lambda-02-iam-policies.png) -->
