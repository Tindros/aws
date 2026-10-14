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
   + Under **Temporary password**, select **Set a permanent password** and enter a unique password of your own that satisfies the policy. Do not reuse example passwords from documentation or commit credentials to the repository.

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

#### Step 2: Test Authentication via the React SPA

The App Client in this workshop is configured for **choice-based sign-in** and **Secure Remote Password (SRP)**. It does not enable the legacy `USER_PASSWORD_AUTH` flow, so do not use a `USER_PASSWORD_AUTH` CLI example here. That command will fail unless you change the App Client configuration, which is not part of this workshop.

```text
Authentication is tested through the React SPA in Section 5.7.1, which uses Cognito's SRP-compatible client flow. After a successful sign-in, inspect the returned ID and access tokens in the browser's developer tools or application debugging output. Never paste real tokens into a public issue, repository, or screenshot.
```

After successful sign-in through the SPA, Cognito returns an authentication result containing tokens. The exact response depends on the selected sign-in flow. Treat the following as a redacted illustration, not output to copy:

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
   + **Authorization (AuthZ - "What you are allowed to do"):** The **Access Token (`AccessToken`)** is intended for authorized API access and contains the `token_use: "access"` claim and any applicable scopes.

2. **Why Our Architecture Sends the Access Token to API Gateway:**
   + The Cognito ID Token describes the authenticated user to the client application. The Access Token is the appropriate token to present to an API.
   + For an HTTP API JWT authorizer, API Gateway checks the `aud` claim when present; if `aud` is absent, it can check `client_id`. Cognito access tokens commonly use `client_id`, so configure the authorizer audience with the App Client ID and send the Access Token in `Authorization: Bearer <TOKEN>`.
   + API Gateway validates the token signature, issuer, expiration, and audience/client identifier before passing verified claims such as `sub` to Lambda.

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
**Security Foundation:** The `sub` claim is the stable user identifier within the User Pool. Lambda must use the `sub` claim supplied by API Gateway after token validation—not a `userId` sent in the request body—to scope DynamoDB operations to the authenticated user.
{{% /notice %}}

---

#### Next Step

Proceed to [Amazon DynamoDB Table & Key Design](../../5.4-DynamoDB/) to configure the serverless database.
