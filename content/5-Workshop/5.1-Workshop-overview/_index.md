---
title : "Introduction & Architecture"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### Project Context

Managing personal finances effectively requires quick logging, clear categorization, and instant visibility into monthly spending habits. Traditional approaches involving physical notebooks or spreadsheets often suffer from manual calculation errors and poor accessibility across devices. On the other hand, deploying traditional continuously running servers (such as an always-on EC2 instance or container cluster) introduces disproportionate administrative overhead and unnecessary idle infrastructure expenses for a personal utility.

The **Serverless Personal Expense Tracker** solves this challenge by leveraging modern AWS managed serverless services. The backend architecture automatically provisions compute and database capacity only when API requests are initiated by the user, achieving near-zero idle cost while maintaining robust enterprise-grade security.

---

#### Solution Architecture

The system follows a decoupled, three-tier cloud-native architecture:
1. **Client / Presentation Layer:** A Single Page Application (SPA) built with React 19, Vite, and TypeScript. Deployed and distributed globally via **AWS Amplify Hosting**.
2. **Authentication & API Management Layer:** 
   + **Amazon Cognito:** Manages user registration, sign-in, password policies, and issues industry-standard JSON Web Tokens (JWT).
   + **Amazon API Gateway (HTTP API):** Serves as the single API entry point, exposing RESTful endpoints, enforcing CORS rules, and validating user JWT claims using a native **JWT Authorizer**.
3. **Compute & Data Persistence Layer:**
   + **AWS Lambda:** Hosts the serverless backend business logic (Node.js 22.x). Validates payload data, enforces user data boundaries using the authenticated `userId` claim, and performs CRUD and summary computations.
   + **Amazon DynamoDB:** A fully managed NoSQL document store operating in On-Demand capacity mode, persisting expense records partitioned by user identity.
   + **Amazon CloudWatch:** Automatically collects Lambda execution logs, runtime metrics, invocation rates, and error traces.
   + **AWS IAM:** Binds the Lambda function to an execution role implementing least-privilege security policies.

![Serverless Personal Expense Tracker Architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.jpeg)

---

#### Architecture Workflow & Data Flow

1. **User Sign-In:** The user opens the web application hosted on AWS Amplify. The browser communicates directly with the **Amazon Cognito User Pool** via SRP (Secure Remote Password) protocol. Upon successful verification, Cognito returns a JWT `idToken` and `accessToken`.
2. **Authorized API Call:** When the user performs an action (e.g., adding an expense or requesting dashboard summaries), the frontend dispatches an HTTPS request to **Amazon API Gateway** with the header `Authorization: Bearer <JWT_TOKEN>`.
3. **JWT Verification:** API Gateway inspects the token against the Cognito User Pool issuer URL and App Client audience. Requests without valid tokens are immediately rejected with an HTTP `401 Unauthorized` before reaching compute resources.
4. **Lambda Execution:** For verified requests, API Gateway invokes the **AWS Lambda** function through proxy integration, injecting the decoded identity claims (`sub`) into the request context.
5. **Database Interaction:** The Lambda function queries or mutates records in **Amazon DynamoDB** using the user's unique `userId` as the partition key.
6. **Logging & Observability:** Every execution outputs structured logs to **Amazon CloudWatch Logs** for auditing and troubleshooting.

---

#### Workshop Objectives

By completing this workshop, you will:
- Understand how to configure Amazon Cognito for Single Page Applications without exposing client secrets.
- Design a scalable NoSQL single-table schema in Amazon DynamoDB for multi-user isolation.
- Develop an AWS Lambda handler supporting REST CRUD operations and aggregation logic with AWS SDK v3.
- Build an API Gateway HTTP API with CORS configuration and native JWT authorization.
- Integrate a real React/TypeScript frontend with AWS serverless services.
- Establish operational observability with CloudWatch and host the web frontend using AWS Amplify.
- Test authentication barriers and execute an orderly cleanup procedure.
