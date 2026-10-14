---
title : "Amazon API Gateway"
date : 2026-10-14 
weight : 6 
chapter : false
pre : " <b> 5.6. </b> "
---

#### Role of Amazon API Gateway in Serverless Architectures

**Amazon API Gateway** acts as the secure, scalable front door for applications to access backend services. For serverless web applications that require RESTful endpoints and token authentication, **API Gateway HTTP APIs (v2)** offer several compelling advantages over legacy REST APIs:
+ **Significant Cost & Performance Efficiency:** Up to 71% lower cost and up to 70% lower latency compared to REST APIs.
+ **Native OIDC/JWT Authorization:** Built-in JWT authorizers validate incoming Cognito tokens at the gateway layer without requiring custom Lambda authorizer functions.
+ **Simplified CORS Management:** First-class, declarative Cross-Origin Resource Sharing (CORS) configuration, eliminating CORS edge-case bugs common in Single Page Applications.
+ **Native Lambda Proxy Integration:** Automatically formats HTTP requests and maps responses between client browsers and AWS Lambda.

---

#### Section Modules

In this section, you will configure the API Gateway HTTP API, attach the JWT Authorizer, and map the application routes:

1. [Create HTTP API & Configure CORS](5.6.1-create-http-api/)
2. [Configure Cognito JWT Authorizer](5.6.2-jwt-authorizer/)
3. [Define Routes & Lambda Proxy Integration](5.6.3-routes-and-integration/)
