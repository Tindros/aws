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

+ An active **AWS Account** with administrative or deployment privileges.
+ **Target Region:** All workshop resources should be deployed in **US East (N. Virginia) `us-east-1`** for consistent service availability and cross-service integration compatibility.

---

#### 2. Required IAM Permissions

To create and manage the AWS services involved in this workshop (Cognito, DynamoDB, Lambda, API Gateway, IAM, CloudWatch, and Amplify), your IAM user or role must possess adequate permissions.

If you are using a scoped IAM User rather than an Administrator account, attach the following custom policy to your identity:

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
      "Resource": "arn:aws:dynamodb:us-east-1:*:table/ExpenseTable*"
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
      "Resource": "arn:aws:lambda:us-east-1:*:function:expenseTrackerFunction*"
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
      "Resource": "arn:aws:iam::*:role/ExpenseTrackerLambdaRole*"
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
      "Resource": "arn:aws:logs:us-east-1:*:log-group:/aws/lambda/expenseTrackerFunction*:*"
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
