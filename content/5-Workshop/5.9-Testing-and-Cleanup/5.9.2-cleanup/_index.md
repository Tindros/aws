---
title : "Clean Up AWS Resources"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.9.2 </b> "
---

Congratulations on completing the **Serverless Personal Expense Tracker** workshop! 

This workshop walks through a learning implementation of a serverless architecture. Complete each service setup and verification step before describing the system as working:
+ User identity and JWT authentication managed through **Amazon Cognito**.
+ High-performance NoSQL partitioned document storage using **Amazon DynamoDB**.
+ Ephemeral, event-driven compute powered by **AWS Lambda** under strict **IAM least-privilege** permissions.
+ RESTful HTTP routing, CORS controls, and native JWT perimeter validation via **Amazon API Gateway**.
+ Observability and error tracing enabled by **Amazon CloudWatch**.
+ Global frontend delivery and continuous deployment through **AWS Amplify Hosting**.

**Important:** Deleting resources is destructive and may permanently remove expense records and user accounts. Confirm that these are the workshop resources in `us-east-1` and back up anything you need before proceeding. Resource names can differ if you customized the workshop. Follow this cleanup procedure to remove the resources created in this tutorial; deletion does not guarantee that every possible AWS charge has stopped, so review the account's Billing console and check for any remaining resources.

---

#### Step 1: Delete AWS Amplify Application

1. Open the [AWS Amplify Console](https://us-east-1.console.aws.amazon.com/amplify/home?region=us-east-1).
2. Select `expense-tracker-frontend`.
3. In the left navigation pane, choose **App settings** -> **General**.
4. Scroll to the bottom and click **Delete app**.
5. Type `delete` to confirm and click **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-01-delete-amplify.png`  
*(To be captured during deployment: Deleting the frontend application in the AWS Amplify Console)*
{{% /notice %}}
<!-- ![Delete Amplify App](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-01-delete-amplify.png) -->

---

#### Step 2: Delete Amazon API Gateway HTTP API

1. Open the [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
2. Locate `ExpenseTrackerApi` in your APIs list.
3. Select the radio button or checkbox next to `ExpenseTrackerApi`.
4. Click **Delete** in the top action bar.
5. Confirm deletion by typing the API name or clicking **Confirm**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-02-delete-apigw.png`  
*(To be captured during deployment: Deleting ExpenseTrackerApi in the API Gateway Console)*
{{% /notice %}}
<!-- ![Delete API Gateway](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-02-delete-apigw.png) -->

---

#### Step 3: Delete AWS Lambda Function

1. Open the [AWS Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
2. Select the checkbox next to `expenseTrackerFunction`.
3. Choose **Actions** -> **Delete**.
4. Type `delete` in the confirmation dialog and choose **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-03-delete-lambda.png`  
*(To be captured during deployment: Deleting expenseTrackerFunction in the Lambda Console)*
{{% /notice %}}
<!-- ![Delete Lambda Function](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-03-delete-lambda.png) -->

---

#### Step 4: Delete IAM Role & Policies

1. Open the [AWS IAM Console](https://console.aws.amazon.com/iam/home#/roles).
2. Search for and click on `ExpenseTrackerLambdaRole`.
3. Under the **Permissions** tab:
   + Expand the inline policy `ExpenseTrackerDynamoDBPolicy` and choose **Delete**.
   + Select `AWSLambdaBasicExecutionRole` and choose **Remove**.
4. At the top of the role summary page, click **Delete role**.
5. Confirm deletion.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-04-delete-iam-role.png`  
*(To be captured during deployment: Deleting ExpenseTrackerLambdaRole in the IAM Console)*
{{% /notice %}}
<!-- ![Delete IAM Role](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-04-delete-iam-role.png) -->

---

#### Step 5: Delete Amazon DynamoDB Table

1. Open the [Amazon DynamoDB Console](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables).
2. In the Tables list, select `ExpenseTable`.
3. Click **Delete**.
4. In the confirmation dialog, type `confirm` (or `delete`) and choose **Delete table**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-05-delete-dynamodb.png`  
*(To be captured during deployment: Deleting ExpenseTable in the DynamoDB Console)*
{{% /notice %}}
<!-- ![Delete DynamoDB Table](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-05-delete-dynamodb.png) -->

---

#### Step 6: Delete Amazon Cognito User Pool

1. Open the [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
2. Select `expense-tracker-user-pool`.
3. Choose **Delete user pool**.
4. Type the user pool name `expense-tracker-user-pool` in the confirmation dialog and click **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-06-delete-cognito.png`  
*(To be captured during deployment: Deleting expense-tracker-user-pool in the Cognito Console)*
{{% /notice %}}
<!-- ![Delete Cognito User Pool](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-06-delete-cognito.png) -->

---

#### Step 7: Delete CloudWatch Log Group

1. Open the [Amazon CloudWatch Console](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1).
2. In the navigation pane, expand **Logs** and choose **Log groups**.
3. Select the checkbox next to `/aws/lambda/expenseTrackerFunction`.
4. Choose **Actions** -> **Delete log group(s)**.
5. Confirm deletion by choosing **Delete**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-07-delete-cloudwatch.png`  
*(To be captured during deployment: Deleting /aws/lambda/expenseTrackerFunction log group)*
{{% /notice %}}
<!-- ![Delete CloudWatch Log Group](/images/5-Workshop/5.9-Testing-and-Cleanup/cleanup-07-delete-cloudwatch.png) -->

{{% notice tip %}}
**Verify cleanup:** Confirm each listed resource is deleted in `us-east-1`, then review AWS Billing and search for any remaining workshop resources. This checklist covers only the resources listed above and cannot guarantee that the account has no other billable resources.
{{% /notice %}}
