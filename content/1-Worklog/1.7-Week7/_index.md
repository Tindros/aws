---
title: "Week 7 Worklog"
date: 2026-10-19
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

**Week 7 Objectives:**

* Complete the Serverless backend for the Expense Tracker.
* Build the Lambda business logic and connect it to DynamoDB.
* Configure API Gateway and JWT authentication.

**Tasks to be completed this week:**

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Create the expenseTrackerFunction Lambda function.<br>- Configure the Node.js runtime and DynamoDB table environment variable. | 20/10/2026 | 20/10/2026 | [Serverless Backend with Lambda, S3, and DynamoDB](https://cloudjourney.awsfcaj.com/) |
| 3 | - Implement GET, POST, GET by ID, PUT, DELETE, and summary operations.<br>- Connect Lambda to DynamoDB. | 21/10/2026 | 21/10/2026 | [Serverless CRUD with Lambda and DynamoDB](https://cloudjourney.awsfcaj.com/) |
| 4 | - Create an HTTP API with API Gateway.<br>- Create the Lambda integration and expense routes.<br>- Configure CORS. | 22/10/2026 | 22/10/2026 | [Build Frontend to call API Gateway](https://cloudjourney.awsfcaj.com/) |
| 5 | - Configure the Cognito JWT Authorizer.<br>- Set the issuer and audience using the Cognito User Pool and App Client. | 23/10/2026 | 23/10/2026 | [Authentication with Amazon Cognito](https://cloudjourney.awsfcaj.com/) |

**Results achieved in Week 7:**

* Successfully created and configured the expenseTrackerFunction Lambda function.
* Implemented CRUD operations with DynamoDB.
* Implemented the summary API for total expenses, count, and spending by category.
* Created the HTTP API and connected API Gateway to Lambda.
* Configured the Cognito JWT Authorizer for the API.
* Protected the API routes with JWT authentication.
* Completed the main backend of the application.
