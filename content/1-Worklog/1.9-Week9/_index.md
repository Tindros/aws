---
title: "Week 9 Worklog"
date: 2026-11-02
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

**### Week 9 Objectives:**

* Test and stabilize the complete system.
* Verify JWT authentication, authorization, and user data isolation.
* Review and complete the Workshop documentation.

**### Tasks to be completed this week:**

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Test Lambda with GET, POST, PUT, DELETE, and summary operations.<br>- Review DynamoDB data access. | 03/11/2026 | 03/11/2026 | [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| 3 | - Test JWT authentication and the API Gateway authorizer.<br>- Verify requests with valid and invalid authentication. | 04/11/2026 | 04/11/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) |
| 4 | - Test data isolation between users.<br>- Verify that Lambda uses the Cognito sub claim to access the correct user's data. | 05/11/2026 | 05/11/2026 | [Authentication with Amazon Cognito](https://000081.awsstudygroup.com/) · [Serverless CRUD with Lambda and DynamoDB](https://000133.awsstudygroup.com/) |
| 5 | - Review Workshop pages from 5.1 to 5.9.<br>- Check consistency between the Vietnamese and English versions. | 06/11/2026 | 06/11/2026 | [The First Cloud Journey](https://cloudjourney.awsstudygroup.com/) |

**### Results achieved in Week 9:**

* Successfully tested the CRUD APIs and summary API.
* Verified JWT authentication and API Gateway authorization.
* Confirmed user data isolation through the Cognito sub claim.
* Verified that one user cannot access another user's expense data.
* Tested DELETE behavior for data outside the user's ownership.
* Reviewed the main Workshop content from 5.1 to 5.9.
* Maintained consistency between the Vietnamese and English versions.
