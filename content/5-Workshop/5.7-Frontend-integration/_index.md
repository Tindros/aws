---
title : "React Frontend Integration"
date : 2026-10-14 
weight : 7 
chapter : false
pre : " <b> 5.7. </b> "
---

#### Transitioning from Local Prototype to Live AWS Backend

The existing **Serverless Personal Expense Tracker** frontend is an interactive Single Page Application developed with **React 19**, **Vite**, and **TypeScript**. During early development, the application operated using local mock services (`authService.ts` and `expenseService.ts`) with hardcoded arrays and simulated network delays.

In this section, you will wire the frontend codebase to the deployed AWS cloud infrastructure:
1. **Environment Configuration:** Inject AWS deployment parameters into Vite environment variables (`.env`).
2. **Real Authentication:** Replace the mock login/register logic with the official Amazon Cognito SDK (`amazon-cognito-identity-js` or AWS Amplify Auth), storing the returned JWT tokens in local browser storage.
3. **Real REST Endpoints:** Connect the expense service to the Amazon API Gateway HTTP API Invoke URL, automatically appending the `Authorization: Bearer <TOKEN>` header to all outgoing requests.

---

#### Section Modules

1. [Connect Amazon Cognito Authentication](5.7.1-cognito-auth/)
2. [Connect API Gateway & DynamoDB Endpoints](5.7.2-api-service/)
