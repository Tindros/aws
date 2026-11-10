---
title: "Worklog Week 6"
date: 2026-10-12
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**### Week 6 Objectives:**

* Design the AWS architecture for the Serverless Personal Expense Tracker.
* Start implementing the required AWS services.
* Set up the database and user authentication mechanism.

**### Tasks to be completed this week:**

| Day | Task | Start Date | Completion Date | Resources |
| --- | --- | --- | --- | --- |
| 2 | - Review the Serverless Personal Expense Tracker architecture.<br>- Define the roles of Cognito, API Gateway, Lambda, DynamoDB, CloudWatch, and IAM. | 13/10/2026 | 13/10/2026 | |
| 3 | - Create and configure an Amazon Cognito User Pool.<br>- Create an App Client and configure authentication for the frontend. | 14/10/2026 | 14/10/2026 | |
| 4 | - Create an Amazon DynamoDB table for expense data.<br>- Design the partition key and sort key using userId and expenseId. | 15/10/2026 | 15/10/2026 | |
| 5 | - Create an IAM Role for Lambda.<br>- Configure permissions for DynamoDB and CloudWatch Logs. | 16/10/2026 | 16/10/2026 | |

**### Results achieved in Week 6:**

* Completed the Serverless architecture for the Personal Expense Tracker.
* Successfully created the Cognito User Pool and App Client.
* Created the `ExpenseTable` DynamoDB table.
* Designed the data structure using `userId` as the partition key and `expenseId` as the sort key.
* Created the Lambda Execution Role and configured the required permissions.
* Gained a clear understanding of the authentication flow from the frontend to Cognito and JWT-based API protection.