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
  -H "Authorization: Bearer <ACCESS_TOKEN>"
```

3. **Expected Result:**
   + HTTP status code: `200 OK`.
   + Response headers include the `Access-Control-Allow-Origin` value configured in API Gateway (for a restricted setup, it should match the frontend origin).
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
**Verify that Lambda was not invoked:** Open CloudWatch Logs for `expenseTrackerFunction` and check the timestamps around your test. Requests rejected by the API Gateway JWT authorizer should not invoke Lambda or create Lambda log entries. API Gateway requests may still incur charges; this is not a claim of zero total cost.
{{% /notice %}}

---

#### Test 3: Multi-User Data Isolation Verification

Verify that users cannot inspect, modify, or delete another user's expenses:

1. In the Cognito Console, create a second user: `user-b@example.com`.
2. Sign in as `user-b@example.com` via the frontend application.
3. Add a new expense (e.g., Description: `User B Lunch`, Amount: `20.00`).
4. Sign out and sign back in as `demo-user@example.com`.
5. Observe the **Expenses** table and **Dashboard**:
   + `demo-user@example.com` sees only their own expense records. The record created by User B is not listed.
6. Copy the `expenseId` of User B’s test expense from a trusted test response or the DynamoDB console. While signed in as User A, try `GET /expenses/<USER_B_EXPENSE_ID>`, then try `PUT` and `DELETE` for that same ID. Each request must use User A’s Access Token. Expect no access to User B’s item (GET should return `404`; update should return `404`; delete may return success with this tutorial handler, but must not delete User B’s item).
7. Sign back in as User B and verify that the item still exists. This confirms the negative update/delete checks did not affect User B’s data.
   + **Security Architecture Explanation:** The Lambda function uses `event.requestContext.authorizer.jwt.claims.sub`, supplied by the configured JWT authorizer, as the partition key for reads, updates, and deletes. Isolation depends on every operation consistently using this verified identity and never accepting `userId` from the request body.
