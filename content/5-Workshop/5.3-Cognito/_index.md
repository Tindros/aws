---
title : "Amazon Cognito Setup & Authentication"
date : 2026-10-14 
weight : 3 
chapter : false
pre : " <b> 5.3. </b> "
---

#### Role of Amazon Cognito in Serverless Architecture

In a secure cloud application, user identity management, credential hashing, session persistence, and token issuance should adhere to rigorous industry security standards. **Amazon Cognito** provides a managed customer identity and access management (CIAM) service, so the application does not need to build its own user directory or implement password hashing and token-signing logic.

In the **Serverless Personal Expense Tracker**, Amazon Cognito fulfills several critical functions:
+ **User Directory & Registration:** Provides a secure directory (User Pool) where users register and authenticate using their email address.
+ **Token-Based Authentication:** Issues signed OpenID Connect (OIDC) compliant JSON Web Tokens (**ID Token** and **Access Token**) containing the user's immutable subject identifier (`sub`).
+ **Decoupled Security:** The client browser directly exchanges credentials with Cognito over HTTPS using the Secure Remote Password (SRP) protocol. The application backend never sees or stores cleartext passwords.
+ **API Gateway Authorization:** The issued JWT tokens are validated by Amazon API Gateway's native JWT authorizer to enforce access boundaries before any backend Lambda function executes.

---

#### Section Modules

In this section, you will configure the Cognito User Pool and verify its token output:

1. [Create Cognito User Pool & App Client](5.3.1-create-user-pool/)
2. [User Registration, Authentication & JWT Verification](5.3.2-test-auth/)
