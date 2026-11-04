---
title : "Amazon API Gateway"
date : 2026-10-14
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Role of Amazon API Gateway in Serverless Architectures

**Amazon API Gateway** provides a managed entry point for applications to communicate with backend services. For this Expense Tracker, **HTTP APIs (API Gateway v2)** provide the features needed to connect the React frontend to AWS Lambda:

- **JWT authorization:** Native JWT authorizers can validate tokens issued by identity providers such as Amazon Cognito.
- **CORS configuration:** Configure cross-origin access for the local development frontend and the deployed Amplify application.
- **Lambda proxy integration:** Forward HTTP requests to the Lambda function and return its response to the client.
- **Automatic deployment:** An HTTP API stage can automatically deploy route and integration changes when auto-deploy is enabled.

---

#### Section Modules

In this section, you will configure the HTTP API, create the Cognito JWT authorizer, connect the application routes to Lambda, and obtain the API endpoint:

1. [Create HTTP API & Configure CORS](5.6.1-create-http-api/)
2. [Configure Cognito JWT Authorizer](5.6.2-jwt-authorizer/)
3. [Define Routes & Lambda Proxy Integration](5.6.3-routes-and-integration/)
