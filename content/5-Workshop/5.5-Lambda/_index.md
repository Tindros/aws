---
title : "AWS Lambda Implementation & Execution Role"
date : 2026-10-14 
weight : 5 
chapter : false
pre : " <b> 5.5. </b> "
---

#### Role of AWS Lambda in Serverless Compute

**AWS Lambda** is an event-driven, serverless computing service that executes backend business logic without requiring server provisioning, OS patching, or persistent daemon processes. Lambda automatically scales dynamically from zero to thousands of concurrent requests, executing your application code only when triggered by HTTP requests from Amazon API Gateway.

In the **Serverless Personal Expense Tracker**, AWS Lambda acts as the centralized controller:
+ **Identity Extraction:** Extracts the verified user subject ID (`sub`) directly from the Cognito JWT claims injected into the API Gateway proxy request context.
+ **Validation & Sanitization:** Validates request payloads (amount, category, date, description) before executing database transactions.
+ **DynamoDB Operations:** Executes scoped queries and point-lookups against `ExpenseTable` using the AWS SDK for JavaScript v3 (`@aws-sdk/client-dynamodb` and `@aws-sdk/lib-dynamodb`).
+ **Aggregation & Metrics:** Computes real-time dashboard analytics (total spent, monthly spending, category breakdown, top spending category).
+ **Least-Privilege Execution:** Executes under a dedicated IAM role restricted exclusively to necessary DynamoDB actions and CloudWatch log groups.

---

#### Section Modules

1. [Create IAM Lambda Execution Role](5.5.1-iam-role/)
2. [Implement Expense Tracker Lambda Function](5.5.2-create-function/)
