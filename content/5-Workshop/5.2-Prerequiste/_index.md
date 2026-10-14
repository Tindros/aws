---
title : "Prerequisites"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

Before beginning this workshop, ensure that you have configured the required AWS account access, IAM permissions, and local software development environment.

---

#### 1. AWS Account & Region Selection

+ An active **AWS Account**. Use an IAM Identity Center user, IAM role, or other administrator-approved identity with only the permissions needed for this workshop. Do not use the AWS account root user for routine work.
+ **Target Region:** All workshop resources should be deployed in **US East (N. Virginia) `us-east-1`** for consistent service availability and cross-service integration compatibility.

---

#### 2. Required IAM Permissions

To create and manage the AWS services involved in this workshop (Cognito, DynamoDB, Lambda, API Gateway, IAM, CloudWatch, and Amplify), your IAM user or role must possess adequate permissions.

The policy below is an illustrative starting point for a dedicated training account, not a guaranteed complete or least-privilege policy. Some AWS Console operations require additional permissions, and broad API Gateway permissions are included here. Prefer a temporary, administrator-approved sandbox role. Do not attach this example to a production identity. Before using it, replace every `<ACCOUNT_ID>` placeholder with your 12-digit AWS account ID.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CognitoManagement",
      "Effect": "Allow",
      "Action": [
        "cognito-idp:CreateUserPool",
        "cognito-idp:DeleteUserPool",
        "cognito-idp:DescribeUserPool",
        "cognito-idp:UpdateUserPool",
        "cognito-idp:CreateUserPoolClient",
        "cognito-idp:DeleteUserPoolClient",
        "cognito-idp:DescribeUserPoolClient",
        "cognito-idp:AdminCreateUser",
        "cognito-idp:AdminSetUserPassword",
        "cognito-idp:AdminDeleteUser",
        "cognito-idp:ListUsers"
      ],
      "Resource": "*"
    },
    {
      "Sid": "DynamoDBManagement",
      "Effect": "Allow",
      "Action": [
        "dynamodb:CreateTable",
        "dynamodb:DeleteTable",
        "dynamodb:DescribeTable",
        "dynamodb:UpdateTable",
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query",
        "dynamodb:Scan"
      ],
      "Resource": "arn:aws:dynamodb:us-east-1:<ACCOUNT_ID>:table/ExpenseTable*"
    },
    {
      "Sid": "LambdaManagement",
      "Effect": "Allow",
      "Action": [
        "lambda:CreateFunction",
        "lambda:DeleteFunction",
        "lambda:GetFunction",
        "lambda:UpdateFunctionCode",
        "lambda:UpdateFunctionConfiguration",
        "lambda:AddPermission",
        "lambda:RemovePermission"
      ],
      "Resource": "arn:aws:lambda:us-east-1:<ACCOUNT_ID>:function:expenseTrackerFunction*"
    },
    {
      "Sid": "ApiGatewayManagement",
      "Effect": "Allow",
      "Action": [
        "apigateway:*"
      ],
      "Resource": "arn:aws:apigateway:us-east-1::*"
    },
    {
      "Sid": "IAMRoleManagement",
      "Effect": "Allow",
      "Action": [
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:GetRole",
        "iam:PassRole",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy",
        "iam:PutRolePolicy",
        "iam:DeleteRolePolicy"
      ],
      "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/ExpenseTrackerLambdaRole*"
    },
    {
      "Sid": "CloudWatchLogsObservability",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:DeleteLogGroup",
        "logs:DescribeLogGroups",
        "logs:DescribeLogStreams",
        "logs:GetLogEvents",
        "logs:FilterLogEvents"
      ],
      "Resource": "arn:aws:logs:us-east-1:<ACCOUNT_ID>:log-group:/aws/lambda/expenseTrackerFunction*:*"
    },
    {
      "Sid": "AmplifyHostingManagement",
      "Effect": "Allow",
      "Action": [
        "amplify:CreateApp",
        "amplify:DeleteApp",
        "amplify:GetApp",
        "amplify:ListApps",
        "amplify:CreateBranch",
        "amplify:StartDeployment"
      ],
      "Resource": "*"
    }
  ]
}
```

{{% notice warning %}}
**Important:** This example uses `apigateway:*` and wildcard resources for some create/list operations, so it is not a strict least-privilege policy. Review and reduce permissions with your AWS administrator before use. Never grant these permissions to the root user or a production identity.
{{% /notice %}}

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.2-Prerequiste/prereq-iam-policy.png`  
*(To be captured during deployment: IAM Console showing the workshop deployment policy attached to the active user)*
{{% /notice %}}
<!-- ![IAM Policy Setup](/images/5-Workshop/5.2-Prerequiste/prereq-iam-policy.png) -->

---

#### 3. Local Development Environment

To work with and test the frontend code and API endpoints locally:

1. **Node.js & Package Manager:**
   + **Node.js:** v20.x or v22.x LTS installed.
   + Verify versions in your terminal:
     ```bash
     node --version
     npm --version
     ```
2. **Frontend Application Source:**
   + The React frontend is available in the project workspace directory `frontend/`.
   + Verify that dependencies install successfully:
     ```bash
     cd frontend
     npm install
     ```
3. **API Testing Utility:**
   + **cURL** (built into Windows PowerShell and macOS/Linux terminals) or **Postman** / **Thunder Client** to issue authenticated HTTP requests with JWT tokens.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.2-Prerequiste/prereq-terminal-env.png`  
*(To be captured during deployment: Terminal output confirming Node.js/npm versions and local frontend setup)*
{{% /notice %}}
<!-- ![Local Environment Setup](/images/5-Workshop/5.2-Prerequiste/prereq-terminal-env.png) -->

---

#### Next Step

Proceed to [Amazon Cognito Setup & Authentication](../5.3-Cognito/) to provision the User Pool and configure authentication.
