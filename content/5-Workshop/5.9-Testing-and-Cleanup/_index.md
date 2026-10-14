---
title : "End-to-End Testing, Security & Resource Cleanup"
date : 2026-10-14 
weight : 9 
chapter : false
pre : " <b> 5.9. </b> "
---

#### Validation & Orderly Teardown

Before completing the workshop, you must verify that the deployed serverless architecture satisfies both functional requirements and security isolation standards. Finally, executing an orderly cleanup avoids ongoing AWS charges for retained resources.

+ **End-to-End & Security Verification:** Validates that API Gateway correctly enforces JWT authorization barriers, returns `401 Unauthorized` for unauthenticated or malformed requests, and guarantees strict tenant isolation in DynamoDB.
+ **Resource Cleanup:** Provides an explicit, dependency-ordered teardown checklist across all 7 provisioned AWS components (Amplify App, API Gateway HTTP API, Lambda function, IAM Role & policies, DynamoDB table, Cognito User Pool, and CloudWatch Log Groups).

---

#### Section Modules

1. [End-to-End & Security Verification](5.9.1-testing/)
2. [Clean Up AWS Resources](5.9.2-cleanup/)
