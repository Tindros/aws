---
title: "Workshop"
date: 2026-10-14
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Serverless Personal Expense Tracker on AWS

#### Overview

The **Serverless Personal Expense Tracker** is a cloud-based application designed to help individuals log, categorize, track, and summarize personal expenditures. It uses managed AWS services so you do not need to provision or maintain virtual machines for the application. Actual availability, scaling behavior, and cost depend on service configuration and usage; serverless does not mean every resource is always free when idle.

In this hands-on workshop, you will step through the planned cloud architecture to implement and integrate every layer of the serverless application:
+ **Authentication & Identity:** Provision an **Amazon Cognito User Pool** with an App Client configured for Single Page Applications (SPA) using secure JWT authentication.
+ **Database Design:** Model and create an **Amazon DynamoDB** table with an On-Demand capacity mode, using partitioned keys for single-tenant data isolation (`userId`) and expense identification (`expenseId`).
+ **Compute & Business Logic:** Implement an **AWS Lambda** function in Node.js 22.x with the AWS SDK v3, bounded by an **IAM Execution Role** enforcing the principle of least privilege.
+ **API Management:** Build an **Amazon API Gateway HTTP API** protected with a native Cognito **JWT Authorizer**, configuring Cross-Origin Resource Sharing (CORS) and integrating Lambda proxy routes.
+ **Frontend Integration:** Connect a modern **React + Vite + TypeScript** frontend MVP with real authentication and REST API endpoints.
+ **Observability & Hosting:** Monitor execution metrics and trace operational logs using **Amazon CloudWatch**, and deploy the production web application using **AWS Amplify Hosting**.
+ **Verification & Cleanup:** Perform end-to-end verification, test unauthorized access protections, and execute an orderly resource teardown.

{{% notice info %}}
**Deployment Status Note:** This workshop serves as the step-by-step implementation guide for the Serverless Personal Expense Tracker. The workshop provides implementation examples and configuration guidance that must be validated in your own AWS account. Review IAM permissions before applying them, and do not deploy resources until you understand the expected costs and cleanup steps. Missing screenshots for unexecuted steps are clearly designated as **`[Pending Deployment]`** and will be populated upon live cloud deployment.
{{% /notice %}}

#### Content

1. [Workshop Overview & Architecture](5.1-Workshop-overview/)
2. [Prerequisites & Environment Setup](5.2-Prerequiste/)
3. [Amazon Cognito Setup & Authentication](5.3-Cognito/)
4. [Amazon DynamoDB Table & Key Design](5.4-DynamoDB/)
5. [AWS Lambda Implementation & Execution Role](5.5-Lambda/)
6. [API Gateway Routes, JWT Authorizer & Integration](5.6-APIGateway/)
7. [React Frontend Integration](5.7-Frontend-integration/)
8. [CloudWatch Monitoring & Amplify Hosting](5.8-Monitoring-and-Amplify/)
9. [End-to-End Testing, Security & Resource Cleanup](5.9-Testing-and-Cleanup/)
