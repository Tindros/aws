---
title : "End-to-End & Security Verification"
date : 2026-10-14 
weight : 1 
chapter : false
pre : " <b> 5.9.1 </b> "
---

In this module, you will execute a series of functional and security verification tests to validate authorization enforcement, error handling, and multi-tenant user isolation.

---

#### Test 1: Authorized API Call Verification

Verify that an API request with a valid Cognito JWT token successfully reaches the Lambda function and queries DynamoDB:

1. Obtain a fresh Cognito **Access Token** from your active session. Send it as `Authorization: Bearer <ACCESS_TOKEN>` when calling the protected API.
2. In your terminal, issue an HTTP `GET` request to your API Gateway Invoke URL:

```bash
curl -i -X GET "https://<API_ID>.execute-api.us-east-1.amazonaws.com/expenses" \
  -H "Authorization: Bearer <VALID_ID_TOKEN>"
```

3. **Expected Result:**
   + HTTP status code: `200 OK`.
   + Response headers include CORS headers (`Access-Control-Allow-Origin: *`).
   + Body contains an array of the user's expense objects retrieved from DynamoDB.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/test-01-curl-authorized.png`  
*(To be captured during deployment: Terminal or Postman showing HTTP 200 OK response with valid JWT token)*
{{% /notice %}}
<!-- ![Authorized Request Verification](/images/5-Workshop/5.9-Testing-and-Cleanup/test-01-curl-authorized.png) -->

---

#### Test 2: Unauthorized Request Rejection

Verify that API Gateway blocks requests lacking a valid authentication header:

1. Send an HTTP `GET` request without an `Authorization` header:

```bash
curl -i -X GET "https://<API_ID>.execute-api.us-east-1.amazonaws.com/expenses"
```

2. **Expected Result:**
   + HTTP status code: `401 Unauthorized`.
   + Response body: `{"message":"Unauthorized"}`.

3. Send an HTTP request with a tampered or expired token:

```bash
curl -i -X GET "https://<API_ID>.execute-api.us-east-1.amazonaws.com/expenses" \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.invalid.token"
```

4. **Expected Result:**
   + HTTP status code: `401 Unauthorized`.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.9-Testing-and-Cleanup/test-02-curl-unauthorized.png`  
*(To be captured during deployment: Terminal showing HTTP 401 Unauthorized rejection for missing/invalid token)*
{{% /notice %}}
<!-- ![Unauthorized Request Rejection](/images/5-Workshop/5.9-Testing-and-Cleanup/test-02-curl-unauthorized.png) -->

{{% notice tip %}}
**Zero Compute Billing:** Open CloudWatch Logs for `expenseTrackerFunction`. Notice that no log stream event was generated for the 401 requests. API Gateway rejected the unauthorized requests at the perimeter, incurring zero Lambda invocation charges.
{{% /notice %}}

---

#### Test 3: Multi-User Data Isolation Verification

Verify that users cannot inspect, modify, or delete another user's expenses:

1. In the Cognito Console, create a second user: `user-b@example.com`.
2. Sign in as `user-b@example.com` via the frontend application.
3. Add a new expense (e.g., Description: `User B Lunch`, Amount: `20.00`).
4. Sign out and sign back in as `demo-user@example.com`.
5. Observe the **Expenses** table and **Dashboard**:
   + `demo-user@example.com` sees only their own expense records. The record created by User B is completely invisible.
   + **Security Architecture Explanation:** The Lambda function queries DynamoDB strictly using `KeyConditionExpression: "userId = :uid"`, where `:uid` is injected by the trusted JWT authorizer from `event.requestContext.authorizer.jwt.claims.sub`. The signature prevents clients from changing verified token claims, but isolation still depends on Lambda consistently using the verified `sub` claim for every read, update, and delete operation.
