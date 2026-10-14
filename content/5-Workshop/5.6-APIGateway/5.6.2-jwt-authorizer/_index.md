---
title : "Configure Cognito JWT Authorizer"
date : 2026-10-14 
weight : 2 
chapter : false
pre : " <b> 5.6.2 </b> "
---

Amazon API Gateway HTTP APIs provide native **JWT Authorizers** that validate OpenID Connect (OIDC) compliant tokens issued by identity providers like Amazon Cognito. By validating tokens directly at the gateway layer, unauthenticated or malicious requests are rejected before invoking backend compute resources, reducing Lambda execution costs and protecting application logic.

---

#### How the JWT Authorizer Protects Your API

When a client sends an HTTP request containing an `Authorization: Bearer <TOKEN>` header:
1. **Signature Verification:** API Gateway automatically fetches the public JSON Web Key Set (JWKS) from Cognito's discovery endpoint (`https://cognito-idp.us-east-1.amazonaws.com/<USER_POOL_ID>/.well-known/jwks.json`) and verifies the cryptographic signature.
2. **Issuer & Expiration Validation:** Ensures the token has not expired (`exp`) and originates from the trusted Cognito User Pool (`iss`).
3. **Audience Check:** Confirms the token was generated specifically for our web frontend app client (`aud` matches the App Client ID).
4. **Context Injection:** Extracts the verified claims (such as `sub` and `email`) and injects them into the Lambda event payload at `event.requestContext.authorizer.jwt.claims`.

{{% notice note %}}
**Why Send the ID Token? (Authentication vs. Authorization)**
* **ID Token vs. Access Token:** In Amazon Cognito, the **ID Token** contains an `aud` (Audience) claim holding the App Client ID, adhering to the OpenID Connect (OIDC) identity standard. In contrast, Cognito **Access Tokens** contain a `client_id` claim instead of an `aud` claim (unless custom resource servers and OAuth scopes are configured). Because API Gateway HTTP API JWT Authorizers validate the `aud` claim against the configured Audience list, the frontend client sends the Cognito ID Token in the `Authorization: Bearer <TOKEN>` header.
* **Authentication (AuthN):** Handled at the gateway boundary by the JWT Authorizer. It verifies *who you are* by checking token signature, issuer, and expiration, and blocks unauthenticated requests from reaching Lambda.
* **Authorization (AuthZ):** Handled in the Lambda business logic and DynamoDB design. It determines *what you can access*. Lambda extracts the verified `sub` (User ID) from the request context and scopes all database operations exclusively to that user partition, ensuring complete multi-tenant tenant isolation.
{{% /notice %}}

---

#### Step-by-Step Configuration

1. In the [Amazon API Gateway Console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1), select `ExpenseTrackerApi`.
2. In the navigation pane under **Develop**, choose **Authorization**.
3. Select the **Manage authorizers** tab, then choose **Create**.
4. Configure the authorizer fields:
   + **Authorizer type:** Select **JWT**.
   + **Name:** Enter `CognitoJWTAuthorizer`.
   + **Identity source:** Enter `$request.header.Authorization`.
   + **Issuer URL:** Enter `https://cognito-idp.us-east-1.amazonaws.com/<YOUR_USER_POOL_ID>` (Replace `<YOUR_USER_POOL_ID>` with your Cognito User Pool ID from Section 5.3.1).
   + **Audience:** Click **Add audience**, then enter your Cognito **App Client ID** (from Section 5.3.1).
5. Choose **Create**.

{{% notice info %}}
📸 **Pending Screenshot:** `static/images/5-Workshop/5.6-APIGateway/apigw-03-jwt-authorizer.png`  
*(To be captured during deployment: Creating JWT Authorizer configured with Cognito Issuer URL and Client ID audience)*
{{% /notice %}}
<!-- ![Configure JWT Authorizer](/images/5-Workshop/5.6-APIGateway/apigw-03-jwt-authorizer.png) -->

{{% notice tip %}}
**Authorizer Ready:** The authorizer is now configured and ready to be attached to your application routes in the next module.
{{% /notice %}}
