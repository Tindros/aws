---
title : "User Registration, Authentication & JWT Verification"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.3.2 </b> "
---

Before integrating the frontend application or deploying backend routes, you should verify that your Cognito User Pool is properly issuing valid JSON Web Tokens (JWT) and understand the structure of the claims.

---

#### Step 1: Create a Test User in the User Pool

You can provision a test account directly from the AWS Console without requiring email confirmation steps:

1. In the [Amazon Cognito Console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1), select `expense-tracker-user-pool`.
2. Select the **Users** tab, then choose **Create user**.
3. In the user creation dialog:
   + Check **Send an email invite** or select **Don't send an email invite** (for immediate local testing).
   + **Email address:** Enter `demo-user@example.com`.
   + Check **Mark email address as verified**.
   + Under **Temporary password**, select **Set a permanent password** and enter a secure password satisfying the policy (e.g., `ExpenseTracker2026!`).

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-06-create-test-user.png`  
*(To be captured during deployment: Creating a test user with verified email in Cognito Console)*
{{% /notice %}}
<!-- ![Create Test User](/images/5-Workshop/5.3-Cognito/cognito-06-create-test-user.png) -->

4. Click **Create user**.
5. Confirm that `demo-user@example.com` appears in the user list with status **Confirmed**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.3-Cognito/cognito-07-test-user-confirmed.png`  
*(To be captured during deployment: User list showing demo-user@example.com with status Confirmed)*
{{% /notice %}}
<!-- ![Test User Confirmed](/images/5-Workshop/5.3-Cognito/cognito-07-test-user-confirmed.png) -->

---

#### Step 2: Test Authentication via CLI

You can simulate the client authentication call using the AWS CLI `initiate-auth` command with your Client ID:

```bash
aws cognito-idp initiate-auth \
  --region us-east-1 \
  --client-id <YOUR_APP_CLIENT_ID> \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=demo-user@example.com,PASSWORD=ExpenseTracker2026!
```

Upon successful authentication, Cognito responds with an `AuthenticationResult` block:

```json
{
  "AuthenticationResult": {
    "AccessToken": "eyJraWQiOiJ...",
    "ExpiresIn": 3600,
    "TokenType": "Bearer",
    "RefreshToken": "eyJraWQiOiJ...",
    "IdToken": "eyJraWQiOiJ..."
  }
}
```

---

#### Step 3: Understanding JWT Token Claims & Authentication vs. Authorization

Amazon Cognito issues two distinct tokens upon successful sign-in:

1. **Authentication vs. Authorization:**
   + **Authentication (AuthN - "Who you are"):** The **ID Token (`IdToken`)** proves the user's verified identity. It is an OpenID Connect (OIDC) token containing identity assertions such as `sub` (unique subject ID), `email`, and `email_verified`.
   + **Authorization (AuthZ - "What you are allowed to do"):** The **Access Token (`AccessToken`)** proves granted permissions and OAuth 2.0 scopes (`token_use: "access"`).

2. **Why Our Architecture Passes the ID Token to API Gateway:**
   + **Audience (`aud`) Verification:** In Amazon Cognito, the **ID Token** includes the Cognito App Client ID in the standard `aud` (Audience) claim (`"aud": "<YOUR_APP_CLIENT_ID>"`). In contrast, standard Cognito Access Tokens represent the caller via `client_id` and do not include an `aud` claim matching the App Client ID (unless custom resource servers and OAuth scopes are configured).
   + Because our API Gateway HTTP API JWT Authorizer is configured with `Audience = <APP_CLIENT_ID>` to prevent tokens from other apps being accepted, API Gateway strictly validates tokens bearing the matching `aud` claim. Passing the ID Token ensures that API Gateway successfully authenticates the caller and forwards verified user identity claims (`sub`) directly to AWS Lambda.

3. **Decoded ID Token Payload Example:**
   ```json
   {
     "sub": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
     "email_verified": true,
     "iss": "https://cognito-idp.us-east-1.amazonaws.com/us-east-1_xxxxxxxxx",
     "cognito:username": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
     "aud": "<YOUR_APP_CLIENT_ID>",
     "event_id": "8f8b...",
     "token_use": "id",
     "auth_time": 1760440000,
     "exp": 1760443600,
     "email": "demo-user@example.com"
   }
   ```

{{% notice tip %}}
**Security Foundation:** Notice the `sub` claim (`"a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d"`). This immutable UUID represents the user across the entire AWS backend. In subsequent modules, our Lambda function will extract this value directly from the verified token to enforce user data boundaries in DynamoDB.
{{% /notice %}}

---

#### Next Step

Proceed to [Amazon DynamoDB Table & Key Design](../../5.4-DynamoDB/) to configure the serverless database.
